# Grammar Checker - User Flow

## 1. Goal

Define how a user interacts with the Grammar Checker from entering Gujarati text to reviewing and applying suggestions.

## 2. Main User Flow

```text
Open Grammar Checker
        ↓
Enter / Paste Gujarati Text
        ↓
Click "Check Grammar"
        ↓
Frontend validates text
        ↓
Request sent to Backend API
        ↓
Grammar + Spelling + AI Analysis
        ↓
Analysis results returned
        ↓
Issues highlighted in Editor
        ↓
Suggestions shown in Right Panel
        ↓
User reviews each suggestion
        ↓
Accept / Reject / Ignore
        ↓
Editor updated when correction is accepted
        ↓
User may use AI Rewrite
        ↓
User saves document
        ↓
User may download document
```

## 3. Step-by-Step Flow

### Step 1 - Open Grammar Checker

The user opens the Grammar Checker from the sidebar.

The application displays:
- Empty editor
- Suggestions panel
- Check Grammar button
- Word count
- Character count

### Step 2 - Enter Text

The user can:
- Type Gujarati text
- Paste Gujarati text
- Edit existing text

The editor updates word and character counts.

### Step 3 - Check Grammar

The user clicks **Check Grammar**.

Frontend performs basic validation:
- Text is not empty
- Text length is within the allowed limit

If validation fails, show a friendly message.

### Step 4 - Analyze Text

The frontend sends the text to the backend.

The backend coordinates:
- Grammar analysis
- Spelling analysis
- Sentence improvement
- AI suggestions

### Step 5 - Display Results

The backend returns structured suggestions.

The frontend:
- Highlights issues in the editor
- Groups suggestions by type
- Displays suggestion cards

### Step 6 - Review Suggestion

Each suggestion contains:
- Original text
- Suggested correction
- Explanation
- Accept
- Reject
- Ignore

### Step 7 - Accept Correction

When the user clicks **Accept**:
1. Replace the original text.
2. Remove or update the corresponding issue highlight.
3. Update the suggestion panel.
4. Update the document state.

### Step 8 - Reject Correction

When the user clicks **Reject**:
1. Keep the original text.
2. Remove the suggestion from the active suggestion list.
3. Keep the text unchanged.

### Step 9 - Ignore Correction

When the user clicks **Ignore**:
1. Keep the original text.
2. Hide the current suggestion.
3. Optionally remember the ignored issue for the current document.

## 4. AI Rewrite Flow

```text
Select Text
    ↓
Click AI Rewrite
    ↓
Choose Rewrite Option
    ↓
Send Request to Backend
    ↓
AI Generates Result
    ↓
Show Preview
    ↓
Accept / Cancel
```

Possible options:
- Make it Simple
- Make it Professional
- Make it Formal
- Improve Sentence
- Shorten
- Expand

## 5. Save Flow

```text
Click Save
    ↓
Validate Document
    ↓
Send Document to Backend
    ↓
Save Document
    ↓
Return Success
    ↓
Show "Saved" status
```

The document should contain:
- User ID
- Title
- Content
- Created date
- Updated date

## 6. Download Flow

```text
Click Download
    ↓
Select Format
    ↓
Generate File
    ↓
Download to User
```

Initial format:
- PDF

Future formats:
- DOCX
- TXT

## 7. Loading State

While analysis is running:
- Show loading indicator
- Disable duplicate Check Grammar requests
- Keep the user informed

Message:

> Analyzing your Gujarati text...

## 8. Error Flow

If the backend or AI service fails:

```text
Request
   ↓
Error
   ↓
Friendly Error Message
   ↓
User Can Retry
```

Example:

> We couldn't analyze the text right now. Please try again.

Technical error details should remain hidden from normal users.

## 9. Important UX Rules

- Never lose the user's original text because of an analysis failure.
- Do not automatically apply corrections without user confirmation.
- Clearly distinguish grammar, spelling, and improvement suggestions.
- Keep the editor usable while suggestions are displayed.
- Show clear feedback after Save and Download.
- Preserve unsaved changes where possible.

## 10. Future Extensions

The flow should later support:
- Voice Input
- OCR
- Translation
- Browser Extension
- Mobile App
- Advanced AI Writing Assistance
