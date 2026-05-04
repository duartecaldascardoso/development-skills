# Organized and Modular React Components

This document outlines an approach to structuring React components that emphasizes modularity, separation of concerns, and maintainability.

## The Core Philosophy: Separation of Concerns

A React component often does much more than just render UI. It handles state, fetches data, reacts to lifecycle events, and defines its own styling. If all of this is stuffed into a single `.tsx` or `.jsx` file, the component quickly becomes difficult to read, test, and maintain.

The solution is to treat a "component" not as a single file, but as a **folder (or package)** containing specialized files for different concerns.

### 1. The Component File (`Component.tsx`)

The `.tsx` (or `.jsx`) file should be strictly reserved for the actual UI presentation. It should focus heavily on the "HTML" side of things (JSX) and the compositional structure of the component.

*   **Do:** Render JSX, pass props, attach event handlers.
*   **Don't:** Define complex business logic, perform heavy data transformations, or write long custom hooks inline.

**Example: `UserProfile/UserProfile.tsx`**

```tsx
import React from 'react';
import { UserProfileProps } from './UserProfile.types';
import { useUserProfile } from './useUserProfile';
import styles from './UserProfile.module.css';

export const UserProfile: React.FC<UserProfileProps> = ({ userId }) => {
  const { user, isLoading, error } = useUserProfile(userId);

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  if (!user) return null;

  return (
    <div className={styles.container}>
      <img src={user.avatarUrl} alt={user.name} className={styles.avatar} />
      <div className={styles.info}>
        <h2>{user.name}</h2>
        <p>{user.email}</p>
      </div>
    </div>
  );
};
```

### 2. Custom Hooks (`useComponent.ts`)

Extract all state management, side effects, and complex logic into a dedicated custom hook file within the component's folder.

This keeps the `.tsx` file clean and allows you to test the logic independently of the UI.

**Example: `UserProfile/useUserProfile.ts`**

```typescript
import { useState, useEffect } from 'react';
import { User } from './UserProfile.types';

export const useUserProfile = (userId: string) => {
  const [user, setUser] = useState<User | null>(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    const fetchUser = async () => {
      try {
        setIsLoading(true);
        // Simulate API call
        const response = await fetch(`/api/users/${userId}`);
        const data = await response.json();
        setUser(data);
      } catch (err) {
        setError(err instanceof Error ? err : new Error('Failed to fetch'));
      } finally {
        setIsLoading(false);
      }
    };

    fetchUser();
  }, [userId]);

  return { user, isLoading, error };
};
```

### 3. Types and Interfaces (`Component.types.ts`)

Define all props, state interfaces, and local data models in a separate file. This avoids cluttering the main component or hook files with type definitions.

**Example: `UserProfile/UserProfile.types.ts`**

```typescript
export interface User {
  id: string;
  name: string;
  email: string;
  avatarUrl: string;
}

export interface UserProfileProps {
  userId: string;
}
```

### 4. Styling (`Component.module.css` or styled-components)

Keep styling tightly coupled to the component but isolated in its own file. Using CSS Modules (`.module.css`) or CSS-in-JS (like styled-components) ensures that styles don't leak out and affect other parts of the application.

### 5. The Barrel File (`index.ts`)

Use an `index.ts` file to cleanly export your component. This allows consumers to import the component from the folder path without needing to know its internal structure.

**Example: `UserProfile/index.ts`**

```typescript
export * from './UserProfile';
export * from './UserProfile.types';
// Optionally export the hook if it's meant to be reusable outside
// export * from './useUserProfile';
```

With this barrel file, you can import the component cleanly elsewhere:

```typescript
// Instead of: import { UserProfile } from './UserProfile/UserProfile';
import { UserProfile } from './UserProfile';
```

## Summary Structure

A complete, modular component structure looks like this:

```
src/
└── components/
    └── UserProfile/
        ├── index.ts              # Exports the public API
        ├── UserProfile.tsx       # UI Presentation (JSX)
        ├── useUserProfile.ts     # Logic, state, and side effects
        ├── UserProfile.types.ts  # TypeScript interfaces and types
        ├── UserProfile.module.css# Component-scoped styles
        └── UserProfile.test.tsx  # Unit tests for the UI and logic
```

By following this pattern, you ensure that as your components grow in complexity, they remain manageable, testable, and easy to understand.
