# Grammar Checker - Screen Specification

## Screen Purpose
The Grammar Checker is the primary writing workspace of the Gujarati AI Writing Assistant. Users can write or paste Gujarati text, check grammar and spelling, review suggestions, apply corrections, and save their document.

## Target Platform
- Primary: Desktop / Laptop
- Secondary: Tablet
- Future: Mobile

## Main Layout
1. Left Sidebar
2. Main Editor
3. Right Suggestions Panel

## Left Sidebar
- Dashboard
- Grammar Checker
- Spell Checker
- AI Rewrite
- Dictionary
- Documents
- Settings
- Profile

The selected module should be visually highlighted.

## Top Header
- Application Logo
- Current Page Name
- Search
- Notification
- User Profile

## Main Editor
Users can:
- Type Gujarati text
- Paste Gujarati text
- Edit and delete text
- Select and copy text

Editor features:
- Gujarati Unicode support
- Text formatting
- Undo / Redo
- Word count
- Character count

## Text Analysis
When the user clicks Check Grammar:
1. Validate input.
2. Send text to backend.
3. Analyze grammar.
4. Analyze spelling.
5. Generate suggestions.
6. Return results.
7. Highlight issues.
8. Display suggestions.

## Error Highlighting
Issue types:
- Grammar Error
- Spelling Error
- Sentence Improvement
- AI Suggestion

Different visual indicators should be used for different issue types.

## Suggestions Panel
Sections:
- Grammar
- Spelling
- Writing Suggestions
- AI Suggestions

Each suggestion displays:
- Original text
- Suggested correction
- Explanation

Actions:
- Accept
- Reject
- Ignore

## Bottom Action Bar
- Check Grammar
- AI Rewrite
- Save
- Download

## AI Rewrite
Possible options:
- Make it professional
- Make it simple
- Make it formal
- Improve sentence
- Shorten
- Expand

## Save
Saved information includes:
- Document title
- Content
- Created date
- Updated date
- User ID

## Download
Initial format:
- PDF

Future formats:
- DOCX
- TXT

## Empty State
Display:
"Start typing or paste Gujarati text here to check grammar."

## Loading State
Display:
"Analyzing your Gujarati text..."

## Error State
Display:
"We couldn't analyze the text right now. Please try again."

Technical error details should not be exposed to users.

## Responsive Behavior
### Desktop
Three-column layout: Sidebar + Editor + Suggestions.

### Tablet
Sidebar + Editor. Suggestions can become collapsible.

### Mobile
Single-column layout. Suggestions appear below the editor or in a drawer.

## Accessibility
The UI should support:
- Keyboard navigation
- Clear focus states
- Readable text
- Accessible buttons
- Sufficient contrast
- Screen-reader-friendly labels

## Future Compatibility
The UI should allow future integration of:
- OCR
- Voice Input
- Speech-to-Text
- Translation
- Advanced AI
- Browser Extension
- Microsoft Word integration
