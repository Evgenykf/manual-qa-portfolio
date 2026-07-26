# Practice Form — Manual Test Cases & Bug Reports

**Module:** Practice Form (XQA.io)
**Precondition:** Site is launched, the Practice Form component is open

## Test Cases

| # | Field | Test Name | Test Description | Expected Result | Actual Result | Pass/Fail | Test Data |
|---|-------|-----------|-------------------|------------------|----------------|-----------|-----------|
| 1 | First Name | Valid input (letters) | 1. Open the website 2. Locate the "First Name" field 3. Enter "Roma" 4. Observe the result | The field accepts the valid name and no error is shown | The field accepted the valid name, no error shown, as expected | Pass | Roma |
| 2 | First Name | Invalid input (numbers, special characters) | 1. Open the website 2. Locate the "First Name" field 3. Enter "Roma@!2" 4. Observe whether a validation error appears | The field should reject numbers and special characters, showing a validation error | No validation error was shown — the field accepted "Roma@!2" as valid | **Fail** | Roma@!2 |
| 3 | First Name | Empty field when submitting form | 1. Open the website 2. Leave the "First Name" field empty 3. Fill in the rest of the form 4. Click "Submit Form" 5. Observe whether the form submits | The form should not submit while First Name is empty; a validation error should appear | The form did not submit while First Name was empty, as expected | Pass | — |
| 4 | Last Name | Valid input (letters) | 1. Open the website 2. Locate the "Last Name" field 3. Enter "Litov" 4. Observe the result | The field accepts the valid name and no error is shown | The field accepted the valid name, no error shown, as expected | Pass | Litov |
| 5 | Last Name | Invalid input (numbers, special characters) | 1. Open the website 2. Locate the "Last Name" field 3. Enter "Litov1/Litolv" 4. Observe whether a validation error appears | The field should reject numbers and special characters, showing a validation error | No validation error was shown — the field accepted "Litov1/Litolv" as valid | **Fail** | Litov1/Litolv |
| 6 | Last Name | Empty field when submitting form | 1. Open the website 2. Leave the "Last Name" field empty 3. Fill in the rest of the form 4. Click "Submit Form" 5. Observe whether the form submits | The form should not submit while Last Name is empty; a validation error should appear | The form did not submit while Last Name was empty, as expected | Pass | — |
| 7 | Email | Valid format | 1. Open the website 2. Locate the "Email" field 3. Enter "ivan.ivanov@gmail.com" 4. Observe the result | The field accepts the valid email format and no error is shown | The field accepted the valid email, no error shown, as expected | Pass | ivan.ivanov@gmail.com |
| 8 | Email | Invalid - missing @ symbol | 1. Open the website 2. Locate the "Email" field 3. Enter "ivn.ivanovgmail.com" 4. Observe whether a validation error appears | The field should reject the email (missing @), showing a validation error | The field correctly rejected the email (missing @), as expected | Pass | ivn.ivanovgmail.com |
| 9 | Email | Invalid - missing domain | 1. Open the website 2. Locate the "Email" field 3. Enter "ivan.ivanov@" 4. Observe whether a validation error appears | The field should reject the email (missing domain), showing a validation error | The field correctly rejected the email (missing domain), as expected | Pass | ivan.ivanov@ |
| 10 | Email | Invalid - multiple @ symbols | 1. Open the website 2. Locate the "Email" field 3. Enter "ivan.ivanov@@@gmail.com" 4. Observe whether a validation error appears | The field should reject the email (multiple @ symbols), showing a validation error | The field correctly rejected the email (multiple @ symbols), as expected | Pass | ivan.ivanov@@@gmail.com |
| 11 | Email | Invalid - Cyrillic characters in domain | 1. Open the website 2. Locate the "Email" field 3. Enter "ivan.ivanov@аычmail.com" 4. Observe whether a validation error appears | The field should reject the email (Cyrillic characters in domain), showing a validation error | No validation error was shown — the field accepted the email with Cyrillic characters as valid | **Fail** | ivan.ivanov@аычmail.com |
| 12 | Gender | Choosing one option removes the other | 1. Open the website 2. Locate the "Gender" radio buttons 3. Select "Male" 4. Select "Female" 5. Observe whether "Male" is automatically deselected | Selecting one Gender option should automatically deselect any previously selected option | Selecting one Gender option correctly deselected the previously selected one, as expected | Pass | — |
| 13 | Gender | Check whether Gender is a mandatory field | 1. Open the website 2. Leave "Gender" unselected 3. Fill in the rest of the form 4. Click "Submit Form" 5. Observe whether the form submits | Determine whether the form allows submission without a Gender selection | The form submitted successfully without any Gender option selected — Gender appears to be an optional field | Pass | — |
| 14 | Mobile | Enter exactly 10 digits | 1. Open the website 2. Locate the "Mobile" field 3. Enter "7900930930" 4. Observe the result | The field accepts exactly 10 digits and no error is shown | The field accepted exactly 10 digits, no error shown, as expected | Pass | 7900930930 |
| 15 | Mobile | Enter less than 10 digits | 1. Open the website 2. Locate the "Mobile" field 3. Enter "79009333" 4. Observe whether a validation error appears | The field should reject fewer than 10 digits, showing a validation error | The field accepted fewer than 10 digits without showing an error | **Fail** | 79009333 |
| 16 | Mobile | Enter more than 10 digits | 1. Open the website 2. Locate the "Mobile" field 3. Enter "7900930930790093093" 4. Observe whether the input is restricted or an error appears | The field should reject more than 10 digits or restrict input to 10 characters | The field accepted more than 10 digits without restricting or showing an error | **Fail** | 7900930930790093093 |
| 17 | Mobile | Try entering letters/symbols | 1. Open the website 2. Locate the "Mobile" field 3. Enter "7900930f1" 4. Observe whether letters/symbols are rejected | The field should reject letters/symbols, allowing digits only | The field accepted letters/symbols instead of digits only | **Fail** | 7900930f1 |
| 18 | Mobile | Copy-paste (Ctrl+V) | 1. Open the website 2. Locate the "Mobile" field 3. Copy the text "sdfasffdsg" and paste it (Ctrl+V) into the field 4. Observe whether the pasted text is rejected | Pasting non-numeric text should be rejected the same way as typing it | Pasting non-numeric text was accepted without validation | **Fail** | sdfasffdsg |
| 19 | Date of Birth | Valid data | 1. Open the website 2. Locate the "Date of Birth" field 3. Enter "09.07.2000" 4. Observe the result | The field accepts the valid date and no error is shown | The field accepted the valid date, no error shown, as expected | Pass | 09.07.2000 |
| 20 | Date of Birth | Non-existent data | 1. Open the website 2. Locate the "Date of Birth" field 3. Enter "33.32.2000" 4. Observe whether a validation error appears | The field should reject a non-existent calendar date (e.g. 33.32.2000), showing a validation error | No validation error was shown — the field accepted the non-existent date "33.32.2000" as valid | **Fail** | 33.32.2000 |
| 21 | Date of Birth | Check whether Date of Birth is a mandatory field | 1. Open the website 2. Leave the "Date of Birth" field empty 3. Fill in the rest of the form 4. Click "Submit Form" 5. Observe whether the form submits | Determine whether the form allows submission without a Date of Birth | The form submitted successfully without a Date of Birth — the field appears to be optional | Pass | — |
| 22 | Date of Birth | The date is too old | 1. Open the website 2. Locate the "Date of Birth" field 3. Enter "09.07.1390" 4. Observe whether a validation error appears | The field should reject an unreasonably old date, showing a validation error | No validation error was shown — the field accepted the unreasonably old date "09.07.1390" as valid | **Fail** | 09.07.1390 |
| 23 | Date of Birth | Empty field when sending | 1. Open the website 2. Leave the "Date of Birth" field empty 3. Fill in the rest of the form 4. Click "Submit Form" 5. Observe whether the form submits | The form should not submit while Date of Birth is empty; a validation error should appear | The form submitted successfully with Date of Birth left empty | **Fail** | — |
| 24 | Hobbies | Is it possible to select several at once? | 1. Open the website 2. Locate the "Hobbies" checkboxes 3. Select "Reading" and "Music" simultaneously 4. Observe whether both remain selected | The field should allow selecting multiple Hobbies checkboxes at once | The field allowed selecting multiple Hobbies checkboxes at once, as expected | Pass | Reading, Music |
| 25 | Hobbies | Check whether Hobbies selection is mandatory | 1. Open the website 2. Leave all "Hobbies" checkboxes unselected 3. Fill in the rest of the form 4. Click "Submit Form" 5. Observe whether the form submits | Determine whether the form allows submission without any Hobbies selected | The form submitted successfully without any Hobbies selected — the field appears to be optional | Pass | — |
| 26 | Picture | Upload the correct format (jpg/png) | 1. Open the website 2. Locate the "Picture" upload field 3. Select the file "photo_2026-06-04_22-12-56.jpg" 4. Observe the result | The field accepts a valid jpg/png image and no error is shown | The field accepted the valid jpg image, no error shown, as expected | Pass | photo_2026-06-04_22-12-56.jpg |
| 27 | Picture | Try downloading an unsupported format | 1. Open the website 2. Locate the "Picture" upload field 3. Select the file "AnyDesk (1).exe" 4. Observe whether the file is rejected | The field should reject unsupported file formats (e.g. .exe), showing a validation error | No validation error was shown — the field accepted the ".exe" file as valid | **Fail** | AnyDesk (1).exe |
| 28 | Picture | Try to download a very large file | 1. Open the website 2. Locate the "Picture" upload field 3. Select the file "Снимок.PNG" (100+ MB) 4. Observe whether a size limit or error appears | The field should either reject files exceeding a reasonable size limit or clearly indicate the maximum allowed size | The field accepted a 100+ MB file with no size limit or warning shown | **Fail** | Снимок.PNG (100+ MB) |
| 29 | Picture | Check whether Picture upload is mandatory | 1. Open the website 2. Leave the "Picture" field empty (no file selected) 3. Fill in the rest of the form 4. Click "Submit Form" 5. Observe whether the form submits | Determine whether the form allows submission without a Picture file | The form submitted successfully without a Picture file — the field appears to be optional | Pass | — |
| 30 | Current Address | Valid data | 1. Open the website 2. Locate the "Current Address" field 3. Enter "742 Evergreen Terrace, Springfield, OR 97477" 4. Observe the result | The field accepts the valid address and no error is shown | The field accepted the valid address, no error shown, as expected | Pass | 742 Evergreen Terrace, Springfield, OR 97477 |
| 31 | Current Address | Very long text | 1. Open the website 2. Locate the "Current Address" field 3. Enter an excessively long text (address repeated ~100 times) 4. Observe whether the input is restricted or an error appears | The field should either accept long text within a reasonable limit or restrict/reject excessively long input | The field accepted an excessively long text string without any restriction or error | **Fail** | 742 Evergreen Terrace, Springfield, OR + (100 words) |
| 32 | Current Address | Check whether Current Address is a mandatory field | 1. Open the website 2. Leave the "Current Address" field empty 3. Fill in the rest of the form 4. Click "Submit Form" 5. Observe whether the form submits | Determine whether the form allows submission without a Current Address | The form submitted successfully without a Current Address — the field appears to be optional | Pass | — |
| 33 | State / City | Check if the list of cities changes depending on the state you select | 1. Open the website 2. Select a "State" (e.g. "Uttar Pradesh") 3. Open the "City" dropdown 4. Observe whether the list of cities corresponds to the selected State | The list of available cities should update based on the selected State | The list of cities did not update based on the selected State | **Fail** | Naryana/NCR/Uttar Pradesh |
| 34 | State / City | Is it possible to select City without selecting State? | 1. Open the website 2. Without selecting a "State", open the "City" dropdown 3. Attempt to select a city (e.g. "Noida"/"Delhi") 4. Observe whether selection is allowed | The City field should not be selectable (or should remain empty/disabled) until a State is selected | A City could be selected without first selecting a State | **Fail** | Noida/Delhi |
| 35 | Submit Form | Submitting a completely empty form | 1. Open the website 2. Leave all fields empty 3. Click "Submit Form" 4. Observe whether the form submits | The form should not submit when completely empty; validation errors should appear for all required fields | The form did not submit when completely empty, as expected | Pass | Minimum data - First Name, Last Name, Email, Mobile, State, City |
| 36 | Submit Form | Submitting with one required field missing | 1. Open the website 2. Fill in only the "First Name" field, leaving all other required fields empty 3. Click "Submit Form" 4. Observe whether the form submits | The form should not submit while any one required field is missing; a validation error should appear for that field | The form submitted with only First Name filled in and all other required fields missing | Pass | First Name |
| 37 | Submit Form | Submitting a fully completed form | 1. Open the website 2. Fill in all fields with valid data 3. Click "Submit Form" 4. Observe whether the confirmation modal appears | The form should submit successfully when all fields are filled in correctly, showing a confirmation | The form submitted successfully with all fields filled in correctly, showing a confirmation, as expected | Pass | Valid data |
| 38 | Keyboard navigation (Tab) | Tab order verification across the form | 1. Open the website 2. Click into the "First Name" field 3. Press Tab repeatedly through the entire form 4. Observe whether focus visibly moves to each field in a logical order | Tab should move focus sequentially and visibly through every interactive element on the form, in a logical order | Tab moved focus sequentially through every field, as expected | Pass | each field can be selected |

## Bug Reports

### Bug #1: First Name and Last Name fields accept numbers and special characters
- **Severity:** Medium
- **Module:** Practice Form
- **Related Test Cases:** #2, #5
- **Precondition:** Site is launched, the Practice Form component is open
- **Steps to Reproduce:**
  1. Enter "Roma@!2" into First Name
  2. Enter "Litov1/Litolv" into Last Name
  3. Submit the form
- **Expected Result:** Both fields should reject numbers and special characters, showing a validation error
- **Actual Result:** Both fields accepted the invalid input with no error shown
- **Status:** New

### Bug #2: Email field accepts domain names with Cyrillic characters (via Punycode)
- **Severity:** Medium
- **Module:** Practice Form (Email)
- **Related Test Case:** #11
- **Precondition:** Site is launched, the Practice Form component is open
- **Steps to Reproduce:**
  1. Enter "ivan.ivanov@аычmail.com" into the Email field
  2. Submit the form
- **Expected Result:** The field should reject the email (Cyrillic characters in domain), showing a validation error
- **Actual Result:** The email was accepted as valid with no warning, despite resolving to a non-existent, visually-spoofed domain
- **Status:** New

### Bug #3: Date of Birth field accepts invalid, future, and unrealistic dates
- **Severity:** High
- **Module:** Practice Form (Date of Birth)
- **Related Test Cases:** #20, #22, #23
- **Precondition:** Site is launched, the Practice Form component is open
- **Steps to Reproduce:**
  1. Enter "33.32.2000" (non-existent date)
  2. Enter "09.07.2040" (future date)
  3. Enter "09.07.1390" (unrealistically old date)
  4. Submit the form after each
- **Expected Result:** The field should reject a non-existent calendar date, a future date, and an unrealistically old date, showing a validation error for each
- **Actual Result:** All three values were accepted as valid with no error shown
- **Status:** New

### Bug #4: Picture upload field accepts executable (.exe) files
- **Severity:** Critical
- **Module:** Practice Form (Picture)
- **Related Test Case:** #27
- **Precondition:** Site is launched, the Practice Form component is open
- **Steps to Reproduce:**
  1. Select "AnyDesk (1).exe" in the Picture upload field
- **Expected Result:** The field should only accept jpg/png files and reject any other format, including executables
- **Actual Result:** The .exe file was accepted with no error or restriction
- **Status:** New

### Bug #5: Picture upload field has no file size limit
- **Severity:** Medium
- **Module:** Practice Form (Picture)
- **Related Test Case:** #28
- **Precondition:** Site is launched, the Practice Form component is open
- **Steps to Reproduce:**
  1. Select a valid PNG file larger than 100 MB in the Picture upload field
- **Expected Result:** The field should either reject files above a reasonable size or clearly indicate a maximum size limit
- **Actual Result:** A 100+ MB file was accepted with no warning or restriction
- **Status:** New

### Bug #6: City dropdown is not linked to the selected State
- **Severity:** High
- **Module:** Practice Form (State / City)
- **Related Test Cases:** #33, #34
- **Precondition:** Site is launched, the Practice Form component is open
- **Steps to Reproduce:**
  1. Select a State (e.g. "Uttar Pradesh")
  2. Open the City dropdown without changing the State
  3. Attempt to select a City without selecting any State first
- **Expected Result:** The City list should update based on the selected State, and City should not be selectable before a State is chosen
- **Actual Result:** The City list does not change based on the selected State, and a City can be selected without selecting a State first
- **Status:** New

### Bug #7: Mobile field does not validate digit count or character type
- **Severity:** High
- **Module:** Practice Form (Mobile)
- **Related Test Cases:** #15, #16, #17, #18
- **Precondition:** Site is launched, the Practice Form component is open
- **Steps to Reproduce:**
  1. Enter "79009333" (8 digits)
  2. Enter "7900930930790093093" (20 digits)
  3. Enter "7900930f1" (letters mixed in)
  4. Paste non-numeric text "sdfasffdsg" via Ctrl+V
- **Expected Result:** The field should enforce exactly 10 digits and reject letters/symbols, including pasted text
- **Actual Result:** All invalid inputs (fewer digits, more digits, letters, pasted non-numeric text) were accepted without any validation error
- **Status:** New

**Note:** Unlike Radio Button, Buttons, and Dynamic Properties, keyboard (Tab) navigation on this form works correctly (Test Case #38) — this narrows the earlier hypothesis from a site-wide accessibility issue to one affecting specific components only.