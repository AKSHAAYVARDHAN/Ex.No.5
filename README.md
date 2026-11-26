# EXP 5: Comparing Prompting Techniques Through Engineering Problem-Solving Scenarios

### Name: Akshaay Vardhan S
### Reg No. : 212224220007
## 1. Project Title

**INVOX – AI-Powered Knowledge Sharing and Professional Networking Platform**

## 2. Aim

To compare different prompting techniques and evaluate their effectiveness in solving real-world engineering problems by providing the same problem to ChatGPT using different prompting approaches.

## 3. Selected Engineering Scenario

**Scenario: Secure File Upload and Ownership Validation using Firebase Storage**

In the Invox platform, users can upload files and share resources with other users. Firebase Authentication is used for user authentication, while Firebase Storage handles file storage.

During implementation, an upload failure occurred because the Firebase Storage security rules required the uploaded file to contain an `ownerId` metadata field matching the authenticated user's UID.

However, the frontend upload function did not include the required metadata.

**Problem Statement:**

How can a secure file upload mechanism be implemented in Invox using Firebase Storage, ensuring that only authenticated users can upload files and that each uploaded file is associated with its rightful owner?

## 4. Base Prompt (Naïve Prompt)

"How can I implement secure file uploads using Firebase Storage in my web application?"

## 5. Improved Prompts Using Different Prompting Techniques

### Technique 1 – Role-Based Prompting

**Prompt:**

"Act as an experienced Firebase developer. Explain how to implement a secure file upload system using Firebase Storage and Firebase Authentication. Ensure that only authenticated users can upload files and that users can access only files they own. Provide implementation steps and relevant code examples."

#### Technique 2 – Context-Based Prompting

**Prompt:**

"I am developing a web application called Invox using React, TypeScript, Firebase Authentication, Firestore, and Firebase Storage.

Users need to upload files securely. My Firebase Storage security rules require an ownerId metadata field that matches the authenticated user's UID. However, my upload function currently does not provide this metadata, resulting in upload failures.

Analyse this situation and suggest a solution that integrates correctly with my existing technology stack."

### Technique 3 – Step-by-Step Prompting

**Prompt:**

"I am implementing secure file uploads in a React and TypeScript application using Firebase.

Solve the following problem step by step:

1. Explain how Firebase Authentication identifies the current user.
2. Explain how file ownership can be maintained using Firebase Storage metadata.
3. Show how to include the authenticated user's UID as ownerId during file uploads.
4. Explain how to configure Firebase Storage security rules.
5. Explain how to validate uploaded files.
6. Describe how to handle upload errors.

Provide a complete implementation with explanations."

### Technique 4 – Constraint-Based Prompting

**Prompt:**

"Design a secure Firebase Storage upload mechanism for my Invox application using React, TypeScript, Firebase Authentication, and Firebase Storage.

Follow these constraints:

- Only authenticated users should upload files.
- Every uploaded file must contain an ownerId metadata field.
- The ownerId must match the authenticated user's UID.
- Users must not be able to upload files on behalf of other users.
- Validate file size and file type.
- Do not expose Firebase credentials or sensitive information.
- Enforce ownership validation through Firebase Storage security rules, not only frontend validation.
- Provide complete, runnable TypeScript code.
- Avoid unnecessary changes to the existing application architecture."

## 6. AI-Generated Outputs

The following are representative ChatGPT responses for the five prompting approaches.

**Output 1 – Naïve Prompt**

Firebase Storage can be used to upload files securely by following these steps:

1. Configure Firebase Authentication.
2. Initialize Firebase Storage.
3. Use the uploadBytes() function to upload files.
4. Configure security rules to restrict access.
5. Use the authenticated user's UID to identify file ownership.

Example:

```typescript
import { ref, uploadBytes } from "firebase/storage";
import { storage } from "./firebase";

async function uploadFile(file: File, uid: string) {
  const fileRef = ref(storage, `uploads/${uid}/${file.name}`);
  await uploadBytes(fileRef, file);
}
```

This provides a basic file upload mechanism, but additional security rules and metadata validation are required.

**Output 2 – Role-Based Prompting**

As a Firebase developer, I recommend implementing a user-based upload architecture.

The main components are:

- Firebase Authentication for identity verification.
- Firebase Storage for file storage.
- Storage metadata for ownership identification.
- Security rules for access control.

The upload function should include the authenticated user's UID in the custom metadata.

```typescript
import { ref, uploadBytes } from "firebase/storage";
import { storage } from "./firebase";

async function uploadFile(file: File, uid: string) {
  const fileRef = ref(storage, `uploads/${uid}/${file.name}`);

  await uploadBytes(fileRef, file, {
    customMetadata: {
      ownerId: uid
    }
  });
}
```

Firebase Storage rules should verify the authenticated user's UID against the uploaded file's metadata.

**Output 3 – Context-Based Prompting**

The problem occurs because the frontend upload function does not provide the metadata required by the Firebase Storage security rules.

The recommended solution is to modify the upload function to include custom metadata.

```typescript
import { ref, uploadBytes } from "firebase/storage";
import { storage } from "./firebase";

async function uploadFile(file: File, uid: string) {
  if (!uid) {
    throw new Error("User must be authenticated.");
  }

  const fileRef = ref(storage, `uploads/${uid}/${file.name}`);

  const metadata = {
    customMetadata: {
      ownerId: uid
    }
  };

  return await uploadBytes(fileRef, file, metadata);
}
```

The Firebase Storage security rules should verify the ownership metadata.

This directly addresses the mismatch between the frontend upload function and the existing security rules.

**Output 4 – Step-by-Step Prompting**

Step 1: Retrieve the authenticated user using Firebase Authentication.

Step 2: Verify that the user is authenticated before starting an upload.

Step 3: Create a storage reference using the user's UID.

Step 4: Attach ownerId as custom metadata.

Step 5: Validate the uploaded file.

Step 6: Enforce ownership checks using Firebase Storage rules.

Example:

```typescript
import {
  ref,
  uploadBytes
} from "firebase/storage";

import { auth, storage } from "./firebase";

async function uploadFile(file: File) {
  const user = auth.currentUser;

  if (!user) {
    throw new Error("Authentication required.");
  }

  const fileRef = ref(
    storage,
    `uploads/${user.uid}/${file.name}`
  );

  const metadata = {
    contentType: file.type,
    customMetadata: {
      ownerId: user.uid
    }
  };

  return await uploadBytes(fileRef, file, metadata);
}
```

**Output 5 – Constraint-Based Prompting**

A secure implementation should validate authentication, file properties, metadata, and ownership.

Example:

```typescript
import {
  ref,
  uploadBytes
} from "firebase/storage";

import { auth, storage } from "./firebase";

async function uploadFile(file: File) {
  const user = auth.currentUser;

  if (!user) {
    throw new Error("User is not authenticated.");
  }

  const allowedTypes = [
    "image/png",
    "image/jpeg",
    "application/pdf"
  ];

  const maxSize = 5 * 1024 * 1024;

  if (!allowedTypes.includes(file.type)) {
    throw new Error("Unsupported file type.");
  }

  if (file.size > maxSize) {
    throw new Error("File exceeds the 5 MB limit.");
  }

  const fileRef = ref(
    storage,
    `uploads/${user.uid}/${file.name}`
  );

  const metadata = {
    contentType: file.type,
    customMetadata: {
      ownerId: user.uid
    }
  };

  return await uploadBytes(fileRef, file, metadata);
}
```

Suggested Firebase Storage security rules:

```javascript
rules_version = '2';

service firebase.storage {
  match /b/{bucket}/o {
    match /uploads/{userId}/{fileName} {
      allow write: if request.auth != null
        && request.auth.uid == userId
        && request.resource.metadata.ownerId
           == request.auth.uid
        && request.resource.size < 5 * 1024 * 1024
        && request.resource.contentType
           in ['image/png', 'image/jpeg',
               'application/pdf'];
    }
  }
}
```

This approach provides multiple security layers and prevents unauthorized uploads.

## 7. Comparison and Evaluation Table

The five prompting approaches were compared based on relevance, accuracy, completeness, clarity, and usefulness.

| Evaluation Criteria | Naïve | Role-Based | Context-Based | Step-by-Step | Constraint-Based |
|---|---|---|---|---|---|
| Relevance | Medium | High | Very High | High | Very High |
| Accuracy | Medium | High | High | High | High |
| Completeness | Low | Medium | High | High | Very High |
| Clarity | Medium | High | High | Very High | High |
| Practical Usefulness | Low | High | Very High | High | Very High |

<img width="1089" height="618" alt="image" src="https://github.com/user-attachments/assets/650ddb77-02b8-4f7a-b511-eed441aba684" />


## 8. Analysis and Observations

**1. Naïve Prompting**

The naïve prompt generated a basic explanation of Firebase Storage. However, it did not identify the specific metadata mismatch causing the upload failure.

**2. Role-Based Prompting**

Assigning ChatGPT the role of an experienced Firebase developer improved the technical depth and produced a more structured solution.

**3. Context-Based Prompting**

Providing the actual project architecture and error scenario helped ChatGPT understand the problem more accurately. It generated a solution directly related to the existing Invox implementation.

**4. Step-by-Step Prompting**

Breaking the problem into smaller tasks improved the organization and clarity of the response. It covered authentication, metadata, validation, security rules, and error handling.

**5. Constraint-Based Prompting**

Defining explicit technical and security requirements produced the most comprehensive solution. It addressed both frontend validation and backend security enforcement.

**Overall Observation:**

Basic prompts generally produced more relevant and detailed answers than the naïve prompt. However, the effectiveness depended on the problem. Simple questions could be answered adequately using naïve prompts, whereas complex engineering problems benefited significantly from contextual information and explicit constraints.

<img width="889" height="549" alt="image" src="https://github.com/user-attachments/assets/0b40e530-5d50-4196-bc32-8212bb08afa9" />


## 9. Final Selected Prompting Technique

**Selected Technique: Context-Based + Constraint-Based Prompting**

This combination was selected because it provides ChatGPT with both the actual engineering context and the technical requirements.

It helps generate solutions that are relevant to the existing architecture while maintaining security, accuracy, and implementation feasibility.

<img width="691" height="508" alt="image" src="https://github.com/user-attachments/assets/f6227e0d-5fc7-4b62-a847-17bc5122d200" />


## 10. Refined / Final Prompt

"I am developing Invox, an AI-powered knowledge-sharing and professional networking platform using React 19, Vite, TypeScript, Firebase Authentication, Firestore, and Firebase Storage.

I encountered a Firebase Storage upload failure because my security rules require request.resource.metadata.ownerId to match the authenticated user's UID. However, my frontend upload function does not include this metadata.

Act as a senior Firebase and TypeScript engineer.

Analyse the problem and provide a production-oriented solution that:

1. Identifies the root cause of the upload failure.
2. Updates the upload function to include ownerId using customMetadata.
3. Retrieves the authenticated user's UID securely.
4. Implements appropriate Firebase Storage security rules.
5. Validates file types and file sizes.
6. Handles upload errors gracefully.
7. Maintains compatibility with my existing React and Firebase architecture.

Provide complete, runnable TypeScript code and explain each important change. Do not unnecessarily redesign or modify the existing UI or application architecture."

## 11. Engineering Validation

The proposed solution can be validated through the following tests:

| Test Case | Expected Result |
|---|---|
| Authenticated user uploads a valid file | Upload succeeds |
| Unauthenticated user attempts upload | Upload denied |
| User uploads without ownerId metadata | Upload denied |
| User provides another user's UID as ownerId | Upload denied |
| File exceeds the maximum size | Upload denied |
| Unsupported file type is uploaded | Upload denied |
| Authenticated user uploads with matching ownerId | Upload succeeds |

**Validation Method:**

1. Implement the updated upload function.
2. Deploy the Firebase Storage security rules.
3. Test uploads using authenticated and unauthenticated accounts.
4. Attempt uploads with missing or incorrect ownership metadata.
5. Verify the results using the Firebase Storage Emulator or a controlled test environment.

The expected results must be confirmed through actual testing before considering the implementation validated.

## 12. Conclusion

The experiment demonstrated that different prompting techniques significantly influence the quality of AI-generated engineering solutions.

The naïve prompt generated a general solution, whereas role-based, context-based, step-by-step, and constraint-based prompting produced more structured and relevant responses.

Among the techniques evaluated, combining context-based and constraint-based prompting was found to be the most suitable approach for solving the selected Invox engineering problem.

Therefore, providing sufficient technical context, clearly defining constraints, and specifying the expected output can improve the accuracy, completeness, clarity, and practical usefulness of ChatGPT responses.

## RESULT

The prompt for the above-mentioned engineering problem was executed successfully. Different prompting techniques were compared, and the effectiveness of each technique was evaluated based on relevance, accuracy, completeness, clarity, and usefulness.
