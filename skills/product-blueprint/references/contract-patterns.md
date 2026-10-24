# Contract Patterns

The architecture tab shows the one seam that is hardest to get right, written as a contract, an enum and an invokable
action, followed by the Pest tests that prove it. Adapt the names to the product. The shape stays the same.

## Driver Contract

```php
interface TargetDriverContract
{
    /** Platform rules checked before anything is queued. */
    public function validate(Variation $variation): ValidationResult;

    /** Safe to call once per idempotency key. Throws AmbiguousResultException on timeout/5xx. */
    public function execute(Target $target, Payload $payload): ExecutionResult;

    /** Look for evidence the action already happened. Drives Pending Verification. */
    public function verify(Target $target): ?ExecutionResult;

    public function capabilities(): Capabilities;
}
```

## Status Enum

```php
enum TargetStatus: string
{
    case Queued = 'queued';
    case Scheduled = 'scheduled';
    case Running = 'running';
    case Succeeded = 'succeeded';
    case Failed = 'failed';
    case PendingVerification = 'pending_verification';
    case Cancelled = 'cancelled';

    public function label(): string { /* … */ }
    public function color(): string { /* … */ }
    public function canRetry(): bool { return $this === self::Failed; }
}
```

## Invokable Action (one job per target)

```php
final class ExecuteTarget
{
    public function __invoke(Target $t): void
    {
        Cache::lock("target:{$t->uuid}", 120)->block(5, function () use ($t) {
            $t->refresh();
            if (! $t->version->isApprovedForHash()) throw new ApprovalInvalidated($t);
            if ($t->status->isTerminal()) return;

            $t->transitionTo(TargetStatus::Running);
            try {
                $result = $this->drivers->for($t->platform)->execute($t, Payload::from($t));
                $t->markSucceeded($result->permalink);
            } catch (AmbiguousResultException) {
                $t->transitionTo(TargetStatus::PendingVerification);
                VerifyTarget::dispatch($t)->delay(now()->addMinutes(2));
            } catch (TargetRejected $e) {
                $t->markFailed($e->reason());
            }
        });
    }
}
```

## Pest Tests to Show

```php
it('retries only the failed target', function () {
    FakeDriver::failOnce(Platform::B);
    $record = Record::factory()->approved()->withTargets([Platform::A, Platform::B])->create();

    RunRecord::run($record);
    RetryTarget::run($record->targetFor(Platform::B));

    FakeDriver::assertExecutedTimes(Platform::A, 1);
    FakeDriver::assertExecutedTimes(Platform::B, 1);
});

it('blocks execution when the approved hash no longer matches', function () {
    $record = Record::factory()->approved()->scheduled()->create();
    $record->currentVersion->update(['body' => 'edited after approval']);
    expect(fn () => RunRecord::run($record))->toThrow(ApprovalInvalidated::class);
});
```

## Version-bound Approval

- Approval rows store the approved version's `content_hash`
- Any edit creates a new version in Draft and cancels unexecuted targets
- A version is approved only when every stage's hash matches the current one

## MCP Surface Card

| Group | Examples |
|---|---|
| Read | guidelines, profiles, campaigns or plans, calendar, library, performance summaries |
| Write | `create_draft`, `suggest_variation`, `suggest_owner`, `propose_schedule` |
| Not exposed | approve, publish/execute, retry, connect accounts, read tokens, read personal data (unless opted in) |

Each AI client gets a token bound to one user, so it can never do more than that user. Every call
is logged with client, user, diff and cost, and capped per day and per month.

## Package-worthy Abstractions

- Driver contract + fake driver + Pest helpers
- Version-bound, multi-stage approvals for any model
- Tracking links + UTM builder with click capture
