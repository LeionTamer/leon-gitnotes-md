# Generics

In TypeScript generics, extends acts like a gatekeeper. It tells the compiler: "I will accept any type T, but only if it looks at least like this other type."

```ts
type MyGeneric<T extends ConstraintType> = T;
type MustHaveId<T extends { id: string }> = T;
```

You have unlocked a major part of TypeScript safety. By writing K extends keyof T, you are telling the compiler: "K must be one of the keys found in T."

```ts
function getProp<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key]; // Safe! TypeScript knows 'key' exists on 'obj'
}
```

Take every key from T, keep the same name, but set the type to boolean

```ts
type OptionsFlags<T> = {
  [K in keyof T]: boolean;
};
```

## Example

Given the interface 

```ts
type User = { id: string; name: string };
type Product = { id: number; price: number };
type Message = { text: string }; // No id here!
```

How would you write the ExtractId<T> type definition to achieve the following results?

```ts
type A = ExtractId<User>;    // Should result in: string
type B = ExtractId<Product>; // Should result in: number
type C = ExtractId<Message>; // Should result in: never
```

```ts
// We must declare <T> here 👇
type ExtractId<T> = T extends { id: string | number }
  ? T['id']   // Return the type of the id
  : never;    // Ignore if no id exists
```

# Infer keyword

The infer keyword allows you to declare a temporary type variable inside the extends clause to "capture" a type

```ts
type GetPromiseValueType<T> = T extends Promise<infer U> ? U : never;

// Example
type ExamplePromise = Promise<number>;
type ValueTypeOfExamplePromise = GetPromiseValueType<ExamplePromise>; // number
```

```ts
type GetParameters<T> = T extends (...args: infer P) => any ? P : never;

type ExampleFunction = (a: number, b: string) => void;
type Params = GetParameters<ExampleFunction>; // [number, string]
```

## Sample use of infer with Third Party Libraries

```ts
// Imagine this comes from 'some-fancy-chart-library'
import { ChartGenerator } from 'fancy-charts'; 

// The library might define it like this:
// function ChartGenerator(options: { axes: { x: string; y: string }; data: any[] }) { ... }

// We want to "steal" the type of the 'options' parameter
type GetChartOptions<T> = T extends (options: infer P) => any ? P : never;

// Now we have the type without the library explicitly giving it to us
type MyChartOptions = GetChartOptions<typeof ChartGenerator>;

const mySettings: MyChartOptions = {
    axes: { x: 'Time', y: 'Revenue' },
    data: []
};
```

```ts
interface InternalApiResponse<T> {
    data: T;
    status: number;
    metadata: { timestamp: string };
}

// A generic utility to grab just the 'data' type
type UnpackData<T> = T extends InternalApiResponse<infer D> ? D : T;

// Example Usage:
async function getUser() {
    return {
        data: { id: 1, name: "Alex" },
        status: 200,
        metadata: { timestamp: "2026-02-08" }
    };
}

// Extract the user type { id: number; name: string }
type User = UnpackData<Awaited<ReturnType<typeof getUser>>>;

const currentUser: User = {
    id: 1,
    name: "Alex"
};
```

### Extract API Params and Response

```ts
// Imagine a library with a complex update function
import { updateRecord } from 'data-lib';
// Signature: updateRecord(id: string, patches: Array<{ field: string, value: any }>)

/**
 * This helper reaches into the second argument [1], 
 * checks if it's an array, and infers the type of a single element in that array.
 */
type ExtractPatchType<T> = T extends (id: string, patches: Array<infer P>) => any 
  ? P 
  : never;

type Patch = ExtractPatchType<typeof updateRecord>;

// Now we can safely create a single patch object
const nameUpdate: Patch = {
    field: "username",
    value: "new_alias_2026"
};
```

```ts
// This represents a common pattern in many API packages
type ApiResponse<T> = 
  | { status: 'success'; data: T; timestamp: number }
  | { status: 'error'; message: string; code: number };

// Example: An imported function from a 'userService' package
declare function fetchProfile(id: string): Promise<ApiResponse<{ name: string; email: string }>>;

// --- THE INFER MAGIC ---

/**
 * We want a utility that ONLY extracts the 'data' type 
 * if the status is 'success'.
 */
type ExtractSuccessData<T> = T extends { status: 'success'; data: infer D } 
  ? D 
  : never;

// We use ReturnType to get the Promise, Awaited to unwrap the Promise,
// and then our custom utility to grab the 'data' property.
type ProfileData = ExtractSuccessData<Awaited<ReturnType<typeof fetchProfile>>>;

/* ProfileData is now exactly: { name: string; email: string }
  The error properties (message, code) are completely filtered out.
*/

const userProfile: ProfileData = {
    name: "Jane Doe",
    email: "jane@example.com"
};
```

# Template Literals

```ts
type HandlerNames<T extends string> = `on${Capitalize<T>}`;

// Usage
type T1 = HandlerNames<"click" | "focus">;
// Result: "onClick" | "onFocus"
```

# Mapped Type

```ts
type EventName = "click" | "focus";

type ObjectHandlers = {
  [K in EventName as `on${Capitalize<K>}`]: (e: any) => void
};

/*
Resulting Type:
{
  onClick: (e: any) => void;
  onFocus: (e: any) => void;
}
*/
```

# Data Transformation

```ts
// 1. Helper to capitalize just the first letter
type CapitalizeFirst<S extends string> = S extends `${infer First}${infer Rest}` 
  ? `${Uppercase<First>}${Rest}` 
  : S;

// 2. The recursive "infer" engine
type SnakeToCamel<S extends string> = S extends `${infer Head}_${infer Tail}`
  ? `${Head}${SnakeToCamel<CapitalizeFirst<Tail>>}`
  : S;

// 3. Mapping over an entire API Object
type CamelizeKeys<T> = {
  [K in keyof T as SnakeToCamel<K & string>]: T[K]
};

// --- REAL WORLD API APPLICATION ---

// Imagine this is what the backend (Python/Ruby/SQL) sends you
interface RawUserResponse {
  user_id: number;
  first_name: string;
  is_active_member: boolean;
}

// Transform it instantly!
type User = CamelizeKeys<RawUserResponse>;

/* The result for 'User' is:
   {
     userId: number;
     firstName: string;
     isActiveMember: boolean;
   }
*/

const cleanUser: User = {
    userId: 101,
    firstName: "Alex",
    isActiveMember: true
};
```