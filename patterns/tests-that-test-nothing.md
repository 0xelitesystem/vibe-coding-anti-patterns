# Tests that test nothing

## What it looks like

```typescript
// "Test" that's tautologically true
test('processData works', () => {
  const result = processData([1, 2, 3]);
  expect(result).toBeDefined();
});
```

```python
# Test that mocks the function it's supposed to test
def test_compute_total():
    with mock.patch('mymodule.compute_total', return_value=42):
        assert compute_total([1, 2, 3]) == 42
```

```go
// Test with no assertions
func TestProcess(t *testing.T) {
    process(input)
    // ...test ends, no assertions
}
```

## Why agents do this

The agent's reward signal in training was "tests that pass." It didn't have a strong signal for "tests that fail when the code is wrong." So it generates tests that pass, including tests that pass regardless of what the code does.

## How to detect

- **Run mutation testing.** A test that passes against a deliberately broken version of the code is a fake test. Tools: stryker (JS), mutmut (Python), Pitest (Java), gomutesting (Go).
- **Read each test.** Does the assertion actually check the behavior in the test name?
- **Coverage with sanity check.** 90% coverage with bad tests is worse than 50% coverage with good tests. Coverage isn't the test.
- **Break the code on purpose.** Comment out a check or invert an operator. Do tests fail? If not, they were fake.

## How to prevent

- Specify in the prompt: "Tests must include both positive and negative cases (input that should produce X, input that should NOT produce X). For each test, describe what would cause it to fail."
- For each generated test, ask the agent: "if I delete the body of [function under test], which of these tests fail?" If the answer is "none," the tests are fake.
- Use property-based tests for pure functions where appropriate. They're harder to fake.

## How to recover when you find it

- Don't trust the existing tests. Treat them as suggestions for what to test.
- Write characterization tests that capture actual behavior on real inputs and expected outputs.
- Mark the bad tests as `skip` initially rather than deleting; that way you can see what was claimed to be tested.
- Then write real tests that fail on broken code and pass on correct code.
