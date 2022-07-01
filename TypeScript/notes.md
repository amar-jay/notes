# TypeScript 
#### A guide to some typescript concepts

---

### Generics

TypeScript generics allows to define a type based on the input parameters when called.

 In languages like C# and Java, one of the main tools in the toolbox for creating reusable components is *generics*, being able to create a component that can work over a variety of types rather than a single one. This allows users to consume these components and use their own types.

For instance, in defining a function such as;

```jsx
function FunctionInt(x: number ) : number[] {
	return [x, x]

function FunctionStr(x: number ) : number[] {
	return [x, x]
}
```

It could be singulartly combined as:

```tsx

function Fxn<T>(x: T) : T[] {
	let list = []
	list.push(x).push(x)
	return list
}
```

For a more constrained value, You can use;

 

```jsx
function Fxn<T extends string, number>(x: T) : T[] {
	let list = []
	list.push(x).push(x)
	return list
}
```

### Using Class Types in Generics

When creating factories in TypeScript using generics, it is necessary to refer to class types by their constructor functions. For example,

```tsx
function Fxn<Type>(c: { new (): Type }): Type {
  return new c();
}
```

### The `keyof` type operator

The `keyof` operator takes an object type and produces a string or numeric literal union of its keys. 

### The `typeof` type operator

- JavaScript already has a `typeof` operator you can use in an *expression* context.
- TypeScript intentionally limits the sorts of expressions you can use `typeof` on.

Specifically, it’s only legal to use `typeof` on identifiers (i.e. variable names) or their properties. This helps avoid the confusing trap of writing code you think is executing, but isn’t:

```tsx
// Meant to use = ReturnType<typeof msgbox>
let shouldContinue: typeof msgbox("Are you sure you want to continue?");
//Error: ',' expected
```

### Indexed Access Types

We can use an *indexed access type* to look up a specific property on another type.

```tsx
type Person = { age: number; name: string; alive: boolean };
type Age = Person["age"]; //type number
type Age = Person["age" | "name"]; //type number | string
type I2 = Person[keyof Person]; //type string | number | boolean
type AliveOrName = "alive" | "name";    type I3 = Person[AliveOrName];//type string | boolean
const key = "age";    type Age = Person[key]; //Error: const can't be an index of a type
```

### Module Augmentation

Declaration merging is helpful when you need to augment existing modules with new properties. One use-case for that is when you are adding more fields to a data structure provided by a library. This is relatively common with the `[express](https://expressjs.com/)`, which allows you to create HTTP servers.

When working with `express`, a `Request` and a `Response` object are passed to your request handlers (functions responsible for providing a response to a HTTP request). The `Request` object is commonly used to store data specific to a particular request. For example, you could use it to store the logged user that made the initial HTTP request:

```tsx
const myRoute = (req: Request, res: Response) => {
  res.json({ user:req.user });
}
```

Here, the request handler sends back to the client a `json` with the `user` field set to the logged user. The logged user is added to the request object in another place in the code, using an express middleware responsible for user authentication.

The type definitions for the `Request` interface itself does not have a `user` field, so the above code would give the type error `2339`:

To fix that, you have to create a module augmentation for the `express` package, taking advantage of declaration merging to add a new property to the `Request` interface.

If you check the type of the `Request` object in the `express` type declaration, you will notice that it is an interface added inside a global namespace called `Express`, as shown in documentation from the [DefinitelyTyped repository](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/70c3f0558f295a5bf5ba98d649509da21e5b12ae/types/express-serve-static-core/index.d.ts#L17-L25):

```tsx
declare global {
    namespace Express {
        // These open interfaces may be extended in an application-specific manner via declaration merging.
        // See for example method-override.d.ts (https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/method-override/index.d.ts)
        interface Request {}
        interface Response {}
        interface Application {}
    }
}

```

**Note:** Type declaration files are files that only contain type information. The DefinitelyTyped repository is the official repository to submit type declarations for packages that do not have one. The `@types/<package>` packages available on npm are published from this repository.

To use module augmentation to add a new property to the `Request` interface, you have to replicate the same structure in a local type declaration file. For example, imagine that you created a file named `express.d.ts` like the following one and then added it to the `types` option of your `tsconfig.json`:

```tsx
import 'express';

declare global {
  namespace Express {
    interface Request {
      user: {
        name: string;
      }
    }
  }
}
```

From the TypeScript Compiler point of view, the `Request` interface has a `user` property, with their type set to an object having a single property called `name` of type `string`. This happens because all the declarations for the same interface are merged.

Suppose you are creating a library and want to give the users of your library the option to augment the types provided by your own library, like you did above with `express`. In that case, you are required to export interfaces from your library, as normal `type` declarations do not support module augmentation.

## Conditional Types

Conditional types help describe the relation between the types of inputs and outputs. Conditional types are written in a form of a ternary operator(as in JS), 

```tsx
type IamX<T> = T extends string ? '👿': '😁';
type str = IamX<string>; // "👿"
type notStr = IamX<number>; // "😁"
```

A better Example: 

```tsx
abstract class ApplicationError {
    abstract status: number;
    abstract message: string;
}

class ServerError extends ApplicationError {
    status = 500;
    constructor(public message: string) {
        super();
    }
}

type ErrorType<T extends {
error: ApplicationError | Error
}> = T['error'] extends ApplicationError ? ApplicationError : Error;

type error1 = ErrorType<{error: ApplicationError }>
type error2 = ErrorType<{error: Error }>
```

We can also use this type(ErrorType) as a return type of function. Consider a function that extracts an error from the object and returns that error. The one way to implement that function is to use **function overloading**,

```tsx
function getError(response: {error: ApplicationError}): ApplicationError;
function getError(response: {error: Error}): Error;
function getError(response: {error: ApplicationError | Error}): ApplicationError | Error {
    if (response.error instanceof ApplicationError) {
        return response.error;
    }
    return response.error;    
}
```

### Mapped Types

Mapped types build on the syntax for index signatures, which are used to declare the types of properties which have not been declared ahead of time:

```tsx
type OnlyBoolsAndHorses = {
  [key: string]: boolean | Horse;
};
```

A mapped type is a generic type which uses a union of `PropertyKey` to iterate through keys to create a type:

```tsx
type OptionsFlags<Type> = {
  [Property in keyof Type]: boolean;
};
```

There are two additional modifiers which can be applied during mapping: `readonly` and `?`
 which affect mutability and optionality respectively.

```tsx
//removes readonly property from the type's properties
type CreateMutable<Type> = {
  -readonly [Property in keyof Type]: Type[Property];
};
 
type LockedAccount = {
  readonly id: string;
  readonly name: string;
};
 
type UnlockedAccount = CreateMutable<LockedAccount>;
// Unlock -> {id:string, name:string}

// Removes 'optional' attributes from a type's properties
type Concrete<Type> = {
  [Property in keyof Type]-?: Type[Property];
};
 
type MaybeUser = {
  id: string;
  name?: string;
  age?: number;
};
 
type User = Concrete<MaybeUser>;
      
//type User = { id: string, name: string, age: number }
```

## Key Remapping via`as`

In TypeScript 4.1 and onwards, you can re-map keys in mapped types with an `as` clause in a mapped type:

```tsx
type MappedTypeWithNewProperties<Type> = {
    [Properties in keyof Type as NewKeyType]: Type[Properties]
}
```

You can leverage features like [template literal types](https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html) to create new property names from prior ones:

```tsx
type Getters<Type> = {
    [Property in keyof Type as `get${Capitalize<string & Property>}`]: () => Type[Property]
};

interface Person {
    name: string;
    age: number;
    location: string;
}

type LazyPerson = Getters<Person>;
         
//type LazyPerson = { 
	//getName: () => string; 
	//getAge: () => number; 
//getLocation: () => string; }
```

You can filter out keys by producing `never` via a conditional type:

```
// Remove the 'kind' property
type RemoveKindField<Type> = {
    [Property in keyof Type as Exclude<Property, "kind">]: Type[Property]
};

interface Circle {
    kind: "circle";
    radius: number;
}

type KindlessCircle = RemoveKindField<Circle>;
           type KindlessCircle = {
    radius: number;
}Try

```

You can map over arbitrary unions, not just unions of `string | number | symbol`, but unions of any type:

```tsx
type EventConfig<Events extends { kind: string }> = {
    [E in Events as E["kind"]]: (event: E) => void;
}

type SquareEvent = { kind: "square", x: number, y: number };
type CircleEvent = { kind: "circle", radius: number };

type Config = EventConfig<SquareEvent | CircleEvent>
// type Config = {
    //square: (event: SquareEvent) => void;
    //circle: (event: CircleEvent) => void; }

```

### Further Exploration

Mapped types work well with other features in this type manipulation section, for example here is [a mapped type using a conditional type](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html) which returns either a `true` or `false` depending on whether an object has the property `pii` set to the literal `true`:

```tsx
type ExtractPII<Type> = {
  [Property in keyof Type]: Type[Property] extends { pii: true } ? true : false;
};

type DBFields = {
  id: { format: "incrementing" };
  name: { type: string; pii: true };
};

type ObjectsNeedingGDPRDeletion = ExtractPII<DBFields>;
//type ObjectsNeedingGDPRDeletion = { id: false; name: true }

```
