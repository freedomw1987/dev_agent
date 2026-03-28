# Playwright MCP Testing Reference

Use Playwright MCP for automated UI testing after implementation.

## Core Tools

| Tool | Purpose |
|------|---------|
| `mcp__chrome-devtools__take_snapshot` | Capture page structure for test targeting |
| `mcp__chrome-devtools__navigate_page` | Navigate to pages under test |
| `mcp__chrome-devtools__click` | Click/interact with elements |
| `mcp__chrome-devtools__fill` | Fill form inputs |
| `mcp__chrome-devtools__type_text` | Type text into inputs |
| `mcp__chrome-devtools__take_screenshot` | Visual regression testing |
| `mcp__chrome-devtools__get_console_message` | Capture console errors |
| `mcp__chrome-devtools__evaluate_script` | Execute JS for assertions |
| `mcp__chrome-devtools__list_console_messages` | List all console messages |

## Testing Workflow

1. **Start dev server** - Ensure application is running
2. **Navigate** - Use `navigate_page` to go to test URL
3. **Snapshot** - Capture page structure with `take_snapshot`
4. **Interact** - Click elements, fill forms
5. **Verify** - Check results with `evaluate_script`
6. **Document** - Take screenshots for evidence

## Example: Login Flow Test

```javascript
async () => {
  // Navigate to app
  await navigate_page({ type: "url", url: "http://localhost:3000/login" })

  // Snapshot to find element UIDs
  const snapshot = await take_snapshot()

  // Fill login form
  await fill({ uid: snapshot.emailUid, value: "user@test.com" })
  await fill({ uid: snapshot.passwordUid, value: "password123" })

  // Submit
  await click({ uid: snapshot.submitUid })

  // Verify redirect
  const result = await evaluate_script(() => {
    return { url: window.location.href }
  })

  return result.url.includes("/dashboard")
}
```

## Example: Console Error Check

```javascript
async () => {
  await navigate_page({ type: "url", url: "http://localhost:3000" })

  const messages = await list_console_messages({
    types: ["error"]
  })

  return {
    hasErrors: messages.length === 0,
    errors: messages
  }
}
```

## Bug Fix Workflow

When tests fail:

1. **Analyze** - Review test results, console errors
2. **Locate** - Use `get_console_message` for error details
3. **Read** - Use Claude Code `Read` tool on source files
4. **Fix** - Use Claude Code `Edit` tool to modify code
5. **Retest** - Re-run Playwright tests

## Test Spec Format

Document test cases in feature specs:

```markdown
## Test Cases

### TC-001: User Login
- **URL**: http://localhost:3000/login
- **Steps**:
  1. Navigate to login
  2. Enter email: test@example.com
  3. Enter password: Test123!
  4. Click Submit
- **Expected**: Redirect to /dashboard

### TC-002: Form Validation
- **Steps**:
  1. Click Submit without credentials
- **Expected**: Show validation errors
```

## Best Practices

- Test early and often after each implementation task
- Screenshot on failure for visual evidence
- Keep test cases independent
- Fix one bug at a time, verify each fix
- Document test specs in feature directory
