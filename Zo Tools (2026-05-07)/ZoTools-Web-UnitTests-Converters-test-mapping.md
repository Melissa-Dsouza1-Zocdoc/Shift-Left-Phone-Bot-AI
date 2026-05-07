# ZoTools.Web.UnitTests / Converters - Unit Test Mapping

> Repo: [Zocdoc/zo-tools](https://github.com/Zocdoc/zo-tools)
> Folder: `tests/ZoTools.Web.UnitTests/Converters/`
> Generated: 2026-05-07

This file maps unit tests for Newtonsoft.Json custom converters in the ZoTools Web project.

---

## ToolParametersJsonConverterTests.cs

Tests the custom `ToolParametersJsonConverter` (Newtonsoft.Json `WriteJson`) which serializes a `ToolParameters` object to the JSON Schema shape expected by the OpenAI tool-definition contract: `type`, `properties`, and an optional `required` array. The converter must omit `required` when the list is empty or null and must preserve other fields verbatim. SetUp at line 15 wires up a JsonSerializer / JsonWriter pair used by all cases.

| # | Do we need these? | Test Name | What It Tests | Steps | Summary | Scope |
|---|---|---|---|---|---|---|
| 1 | Yes | WriteJson_WithRequiredParams_SerializesAsRequired | One required param produces a `required` array | Build ToolParameters with one required key; call WriteJson; parse output | `required` array contains the single key | Converter |
| 2 | Yes | WriteJson_WithMultipleRequiredParams_SerializesAllParams | All required params land in the `required` array | Build ToolParameters with multiple required keys; serialize; assert array contents | All required params are emitted in order | Converter |
| 3 | Yes | WriteJson_WithEmptyRequiredParams_OmitsRequiredField | Empty `required` collection -> no `required` key | Build ToolParameters with empty required list; serialize; check JSON does not contain `required` | Empty list is omitted (not emitted as `[]`) | Converter |
| 4 | Yes | WriteJson_WithNullRequiredParams_OmitsRequiredField | Null `required` -> no `required` key | Build ToolParameters with required = null; serialize; check JSON does not contain `required` | Null is omitted | Converter |
| 5 | Yes | WriteJson_WithNullValue_WritesNull | Converter writes JSON null when source value is null | Pass null ToolParameters; serialize | Output is JSON `null` | Converter |
| 6 | Yes | WriteJson_PreservesTypeAndProperties | `type` and `properties` are passed through unchanged | Build full ToolParameters; serialize; verify type=`object`, properties payload preserved | Round-trip preserves shape outside `required` | Converter |

### Suggested Missing Tests

- `CanRead` / `ReadJson` should not be supported (or should be tested if it is). No coverage exists for the deserialization side.
- `CanConvert(typeof(ToolParameters))` returns true and returns false for unrelated types.
- Output property ordering is stable (`type` -> `properties` -> `required`) so OpenAI contract stability is not regressed.
- Property names are camelCase / lowercase as the OpenAI schema spec requires (`type`, `properties`, `required`) regardless of the host JsonSerializer naming strategy.
- A property whose value is itself a nested `JObject` is written by reference (no double-encoding to a string).
- Behavior when a key in `RequiredParams` is not present in `Properties` (currently silently allowed - decide and lock down).
- Behavior with duplicate keys in `RequiredParams`.

### Improvement Suggestions / Irrelevant Tests

- All six tests are valuable and cover real risk in the OpenAI tool contract surface; none should be removed.
- The two "omits required" tests (#3 and #4) could share a `TestCase` source rather than be split, but the current form is fine and reads clearly.
- Consider adding an end-to-end snapshot/Verify test (per CS-006) that serializes a representative `ToolDefinition` and locks the full JSON output. This guards against accidental contract drift more strongly than the per-field assertions currently used.
