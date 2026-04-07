---
name: outlook-email
description: Use when sending emails via outlook.office.com using Playwright MCP. Covers compose flow, body injection, file attachment, and how to minimize context usage across multiple emails.
---

# Sending Email via Outlook Web (Playwright MCP)

## Core Flow (per email)

1. Click **New mail** button
2. Fill **To** field → press `Tab` (confirms external address)
3. Fill **Subject** field
4. Click **Message body** to activate it
5. Inject body HTML via `browser_evaluate`
6. Click **Attach file** → click **Browse this computer** → `browser_file_upload`
7. Click **Send**

## Key Principle: Refs Change Per Compose Window

Every new compose window generates new element refs. Do NOT reuse refs across emails. After each Send, the snapshot shows a new "New mail" ref — save it for the next iteration.

## Body Injection (most reliable method)

Use `evaluate()` with innerHTML + dispatchEvent:

```js
(element) => {
  element.innerHTML = `<div>Hej,</div><div><br></div><div>Body text here...</div>`;
  element.dispatchEvent(new Event('input', {bubbles: true}));
  return 'injected';
}
```

Use HTML entities for Danish chars: `&oslash;` (ø), `&aring;` (å), `&aelig;` (æ), `&Oslash;` (Ø), `&Aring;` (Å).

## Minimizing Context (Critical for Batch Sending)

Snapshots are ~70KB each. Never read them inline. Instead:

```python
# After browser_file_upload or other large snapshot steps:
python3 -c "
import json
with open('/path/to/tool-result.json') as f:
    data = json.load(f)
text = data[0]['text']
for keyword in ['attachment', 'Send', 'e1234']:
    idx = text.find(keyword)
    if idx >= 0:
        print(f'[{keyword}]: ...{text[max(0,idx-30):idx+100]}...')
"
```

Save to file with `browser_snapshot(filename="snap.json")` when you need to inspect state.

## Efficient Ref Discovery

After New mail click, the snapshot is small enough to read inline. Key refs to extract:
- `generic "To" [active] [ref=eXXXX]` — To input
- `textbox "Subject" [ref=eXXXX]`
- `textbox "Message body" [ref=eXXXX]`
- `button "Send" [ref=eXXXX]`
- `button "Attach file" [ref=eXXXX]`

After Send, the snapshot collapses back to inbox. Find new `button "New mail" [ref=eXXXX]` for next email.

## Attachment Flow

```
Click "Attach file" → menu appears
  → Click "Browse this computer" [ref=eXXXX]
  → Modal state: [File chooser]
  → browser_file_upload(paths=["/absolute/path/to/file.pdf"])
```

Verify attachment with grep: look for `listbox "file attachments"` containing the filename.

## To Field Confirmation

After `Tab`, Outlook shows a warning for external addresses:
`"The following recipient is outside your organization: email@domain.com"`
This confirms the address was accepted. No extra click needed.

## Sequence for Batch Emails

```
New mail → [get refs from inline snapshot]
→ type To → Tab
→ type Subject
→ click body → [small snapshot change only]
→ evaluate() inject HTML
→ click Attach → click Browse → file_upload → [grep for attachment + Send ref]
→ click Send → [get new New mail ref from post-send snapshot]
→ repeat
```

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Reusing refs from previous compose | Always get fresh refs after New mail click |
| Reading 70KB snapshot inline | Use python3 grep on saved JSON |
| Forgetting Tab after To field | Tab confirms the address token |
| Using plain text in evaluate() | Use innerHTML with HTML entities for Danish chars |
| Not clicking body before evaluate() | Body must be active/focused first |
