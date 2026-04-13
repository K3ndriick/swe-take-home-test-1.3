# Take-Home Test: Vehicle Inspection System

A full-stack vehicle inspection management system using React + TypeScript (frontend) and Express + TypeScript (backend).

**Recommended Time: around 2 hours** — You may take as long as you need, but please don't overthink it.

---

## Backend Tasks (`/backend`)

### Task 1: Implement `createCheck` controller

The `POST /checks` endpoint controller is not implemented. You need to:

1. Validate request body using `validateCheckRequest(req.body)`
2. Return 400 with `ErrorResponse` format if validation fails
3. Call `checkService.createCheck()` if validation passes
4. Return 201 with the created check

**Verify:** Remove `.skip` from the test `"should create check and return 201"` in `api.test.ts`

### Task 2: Fix the bug in service function `createCheck`

The `hasIssue` flag is not correctly identifying if an inspection record has an issue. Find and fix the bug.

**Verify:** Remove `.skip` from the test `"should set hasIssue to true when any item fails"` in `api.test.ts`

Run `npm test` — all tests should pass.

### Task 3: Complete `deleteCheck` controller

The `DELETE /checks/:id` endpoint is not completed. It will not process the deletion and will return `501` regardless of the success or failure of the deletion. Complete this controller and related service function if applicable.

---

## Frontend Tasks (`/frontend`)

### Task 1: Fix Form Input

Check the form (of Submit Vehicle Inspection Result) to make sure the fields are accepting valid input and address any other bugs.

### Task 2: Change dropdown selection for checklist items

In the form, instead of a dropdown to select the result for checklist items, change them to checkboxes, radio buttons, or any other input type that you think is suitable.

### Task 3: Add Notes Field

In the form, add a textarea for optional notes:

- Maximum 300 characters
- (Optional) Display character counter (e.g., "45/300")
- Include in API request and reset after submission

### Task 4: Implement Toast Notifications

**File:** `src/CheckForm.tsx`

Show visual feedback using the provided `Toast.tsx` and `useToast.ts`:

- Success toast on successful submission
- Error toast on failure
- (Optional) Show a good error message in toast on failure

### Task 5: (Optional) Implement a way to delete an inspection record

Note that this task is **completely optional** and we do not expect you to finish this. However, if you wish, you can implement a way to delete an inspection record, provided that you have completed the `deleteCheck` controller. We will provide feedback if you choose to complete this task.

---

## Questions

Please answer these briefly:

1. **Authentication:** If we need to add authentication to this system, how would you approach it?
I would use session-based cookies to implement authentication into the system. We can use a email + password, followed by 2FA authentication via a x-digit code or magic link sent to the user's email. This provides the admins the ability to delete user sessions as they are stored server-side, giving us more control over logins/logouts in the system. However the tradeoff would be that this method would require server-side storage. We can also add a middleware to help protect more sensitive routes, and check if users are authenticated before allowing access to said protected routes.

2. **Improvements:** What other improvements would you implement if this were going to production or if you have more time?
I would implement a soft delete functionality in the system as this would make the system more resilient against accidental deletes. Input sanitisation can also be achieved before sending data downstream/upstream to the DB or to the frontend by having different interface types, 1 for the DB side and 1 for the frontend side. A PostgreSQL DB can be created to store data, as it allows us to set RLS policies for data access control, and also adheres to Principle of Least Privilege (PoLP) - only giving users the minimum level of permissions necessary to perform their intended functions. In addition, some ideas to assist database when record sizes grow would be indexing to optimise query performance, along with adding pagination.

3. **Tech Stack Experience:** Do you have experience with PHP, Vue.js, or mobile app development (React Native/Flutter)?
Yes, I have experience with CakePHP from my university capstone (Final Year Project Part 1), where I built an e-commerce platform. For mobile development, I built an iOS app using Swift for another capstone project, and I'm currently learning React Native by rebuilding that iOS project - though progress has been slow as I've been focusing on another personal project using the Claude API. I've also built multiple projects using a React/Next.js/Supabase stack, including a custom CMS platform (Final Year Project Part 2) and a rebuilt version of my CakePHP e-commerce system in Next.js. I haven't worked with Vue.js directly, but I'm comfortable picking up new frontend frameworks given my experience across React and Next.js. I've also used React during a previous internship, working on a multi-tool SaaS platform for tradespeople.

4. **AI / Tools:** What tools/assistants did you use while working on this assignment (e.g., GitHub Copilot, ChatGPT, etc.)? We appreciate AI usage, we're interested in _how_ you use these tools.
Claude Code was used throughout the duration of this assignment. It was used for pair programming & guided problem-solving. Instead of just copy pasting solutions from Claude, I used it as an assistant to help me understand the contents of the codebase, where to find files to assist in solving the tasks, and identify relevant files. An example of when I used it was fixing the hasIssue and FAILED bug, where it helped me trace through all the interfaces to find the given enums of OK and FAIL. I also used it to evaluate my solutions to assess if I could be missing any edge cases or proposing more optimised solutions.

5. **Visa Status:** What visa are you currently on?
Currently on 485 Visa with full working rights, expiry in early 2028

6. **Languages:** What language(s) do you speak and what's your proficiency level?
English (Native Speaker), Malay (Fluent), Mandarin (Conversational), Cantonese (Beginner)

> **Tip:** You can write your answers directly in this README.md file below each question.

---

## Submission (How to Submit)

1. Create a **public GitHub repository** for this assignment.
2. Push your code with all changes.
3. **Create at least two pull requests (PRs):** one for backend and one for frontend. You may create more (e.g., each task can be an independent PR). You may merge them into the main branch. We can review and may leave comments on your PRs for feedback.
4. Answer questions above.
5. **Please complete and submit within 3 days** unless otherwise discussed.
6. Send the repository link to **admin@enroute-tech.com**.

---

Good luck!
