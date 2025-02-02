# Create Arrays

Creating an empty array is super easy:

```typescript
const foo:string[] = [];
```

If you want to create an array pre-filled with some content use the ES6 `Array.prototype.fill`:

```typescript
const foo:string[] = new Array(3).fill('');
console.log(foo); // ['','',''];
```

If you want to create an array of a predefined length with calls you can use the spread operator:

```typescript
const someNumbers = [...new Array(3)].map((_,i) => i * 10);
console.log(someNumbers); // [0,10,20];
```

