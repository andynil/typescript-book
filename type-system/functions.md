# Functions

* [Parameter Annotations](functions.md#parameter-annotations)
* [Return Type Annotation](functions.md#return-type-annotation)
* [Optional Parameters](functions.md#optional-parameters)
* [Overloading](functions.md#overloading)

## Functions

The TypeScript type system pays a lot of love to functions, after all they are the core building blocks of a composable system.

### Parameter annotations

Of course you can annotate function parameters just like you can annotate other variables:

```typescript
// variable annotation
var sampleVariable: { bar: number }

// function parameter annotation
function foo(sampleParameter: { bar: number }) { }
```

Here I used inline type annotations. Of course you can use interfaces etc.

### Return type annotation

You can annotate the return type after the function parameter list with the same style as you use for a variable, e.g. `: Foo` in the below example:

```typescript
interface Foo {
    foo: string;
}

// Return type annotated as `: Foo`
function foo(sample: Foo): Foo {
    return sample;
}
```

Of course I used an `interface` here, but you are free to use other annotations e.g. inline annotations.

Quite commonly you don't _need_ to annotate the return type of a function as it can generally be inferred by the compiler.

```typescript
interface Foo {
    foo: string;
}

function foo(sample: Foo) {
    return sample; // inferred return type 'Foo'
}
```

However, it is generally a good idea to add these annotation to help with errors e.g.:

```typescript
function foo() {
    return { fou: 'John Doe' }; // You might not find this misspelling of `foo` till it's too late
}

sendAsJSON(foo());
```

If you don't plan to return anything from a function, you can annotate it as `:void`. You can generally drop `:void` and leave it to the inference engine though.

### Optional Parameters

You can mark a parameter as optional:

```typescript
function foo(bar: number, bas?: string): void {
    // ..
}

foo(123);
foo(123, 'hello');
```

Alternatively you can even provide a default value \(using `= someValue` after the parameter declaration\) which is injected for you if the caller doesn't provide that argument:

```typescript
function foo(bar: number, bas: string = 'hello') {
    console.log(bar, bas);
}

foo(123);           // 123, hello
foo(123, 'world');  // 123, world
```

### Overloading

TypeScript allows you to _declare_ function overloads. This is useful for documentation + type safety purpose. Consider the following code:

```typescript
function padding(a: number, b?: number, c?: number, d?: any) {
    if (b === undefined && c === undefined && d === undefined) {
        b = c = d = a;
    }
    else if (c === undefined && d === undefined) {
        c = a;
        d = b;
    }
    return {
        top: a,
        right: b,
        bottom: c,
        left: d
    };
}
```

If you look at the code carefully you realize the meaning of `a`,`b`,`c`,`d` changes based on how many arguments are passed in. Also the function only expects `1`, `2` or `4` arguments. These constraints can be _enforced_ and _documented_ using function overloading. You just declare the function header multiple times. The last function header is the one that is actually active _within_ the function body but is not available to the outside world.

This is shown below:

```typescript
// Overloads
function padding(all: number);
function padding(topAndBottom: number, leftAndRight: number);
function padding(top: number, right: number, bottom: number, left: number);
// Actual implementation that is a true representation of all the cases the function body needs to handle
function padding(a: number, b?: number, c?: number, d?: number) {
    if (b === undefined && c === undefined && d === undefined) {
        b = c = d = a;
    }
    else if (c === undefined && d === undefined) {
        c = a;
        d = b;
    }
    return {
        top: a,
        right: b,
        bottom: c,
        left: d
    };
}
```

Here the first three function headers are available as valid calls to `padding`:

```typescript
padding(1); // Okay: all
padding(1,1); // Okay: topAndBottom, leftAndRight
padding(1,1,1,1); // Okay: top, right, bottom, left

padding(1,1,1); // Error: Not a part of the available overloads
```

Of course it's important for the final declaration \(the true declaration as seen from inside the function\) to be compatible with all the overloads. This is because that is the true nature of the function calls that the function body needs to account for.

> Function overloading in TypeScript doesn't come with any runtime overhead. It just allows you to document the manner you expect the function to be called in and the compiler holds the rest of your code in check.

### Declaring Functions

> Quick Tip: _Type Declarations_ are how you describe the types of existing implementations.

There are two ways to _declare_ the type of a function without providing an implementation. E.g.

```typescript
type LongHand = {
    (a: number): number;
};

type ShortHand = (a: number) => number;
```

The example above are both _exactly_ equivalent. The differences exist when you want to add overloads. You can only add overloads in the long hand declaration version e.g.

```typescript
type LongHandAllowsOverloadDeclarations = {
    (a: number): number;
    (a: string): string;
};
```

