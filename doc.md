# Code Guidelines

### Updating your branches

Use `git pull --rebase` to update your local branches; Do **_not_** use `git merge` as combining merging and rebasing can result in many undesirable conflicts. To update via rebase:

1. `git rebase` + `last commit of target branch`
2. If conflicts arise, resolve them in your editor, stage the changes, then use `git rebase --continue`
3. Onces all commits have finished rebasing, push your updated branch with `git push --force-with-lease`

If you've made a mistake or something goes wrong during the rebasing process, you can cancel and return your branch to its original state via `git rebase --abort`

### Pull request size | How to break it up

To help prevent pushing code that's not been thouroughly reviewed and give your teamates the confidence they need to approve your work in a timely fashion, break up your large contributions into multiple, smaller PRs.

Unless your contribution consists of only simple naming changes, this can be difficult and time consuming to review.

### Naming Conventions

Always use meaningful names. Long names are better than vague names. It's better to have a name that describes in the best possible way what it is, than having abbreviated or short names that require understanding the code to realize what value they hold or what they do.

### Code

- **React** components should be written in PascalCase, e.g. `MyComponent`

- **Functions** should be written in camelCase, e.g. `myFunction`

- **Variables** should be written in camelCase, e.g. `myVar`

- **Boolean variables** should be written in camelCase, and be self descriptive, indicating they are booleans. e.g. `isVisible` rather than `visible` or `hasShift` rather than `shift`

- **Constants** should be written in UPPER_SNAKE_CASE, e.g. `DATE_FORMAT` or `MAX_RETRIES`

- **Object** (mostly enums) or **constants** should be written in UPPERCASE, e.g.

```typescript
enum UserTypes {
  FACILITY = "facility",
  AGENT = "agent",
  ADMIN = "admin",
}
```

### File naming and exports

When there are multiple files within a directory that need to be exported, create an `index.js` file to export all of those files.

Always match the filename with the primary export of that file. Since we use all named exports, we may have a set of exports equally important or one export that is the main one for that file.

### When default exports are needed

Again, always use named exports as this is not only important for consistency, but also makes searching for files/components easier in your editor; However in specific situations, default exports will be needed, in these situations you can 'convert' a named export to a default export via an `index` file.

```typescript
// index.ts
import { MyNamedExportComponent } from "./MyNamedExportComponent";

export default MyNamedExportComponent;
```

#### Set of exports

Commonly used when grouping functions that are related. In this case we name the file in kebab-case, with something meaningful that denotes what all these functions have in common. A usual case may be a `utils/strings.js`

#### One main export

It's possible to have a main export but also some complementary exports. In this case, the main export is the one that takes precedence and the file is named after it since the main purpose of said file is that export.

```typescript
// File User.ts
export { User, UserTypes };
```

### Folders

Folders should match the main export of the index when referring to classes or components. When grouping multiple files or index file within folder has more than one main export.

```typescript
// path MyComponent/index.ts
export { MyComponent };

// path facilityBreakdown/index.ts
export { AgentService, someCronJob };
```

### Branches

Always use `kebab-case` when naming branches. Name format for feature branches: `{type}/{JIRA_TEAM_ID-TICKET#}/{short-description}`.
Examples: `hotfix/EX-17/drawer-wont-open` `feature/TRAD-42/dogecoin-support`

**Possible types:**

- `feat` or `feature`: A new feature
- `fix`: A bug fix
- `hotfix`: A critical bug in prod
- `docs`: Documentation only changes
- `style`: Changes that do not affect the meaning of the code (white-space, formatting, missing semicolons, etc)
- `refactor`: A code change that neither fixes a bug nor adds a feature
- `perf` or `performance`: A code change that improves performance
- `test`: Adding missing or correcting existing tests

### Eslint

Eslint is set up to provide linting rules and improve code guidelines. Remember to run `npm run lint` before committing and pushing changes.

### Use `const` instead of `let` when possible

If you're not going to mutate the variable, use `const` rather than `let` as this will aid in a reader's understanding of intent of your code.

### Keep boolean expressions simple

Keep simple logic for boolean expressions.

```typescript
// Not Preferred
if (user === null)

// Preferred
if (!user)

// Not Preferred
if (user && user.name === null)

// Preferred
if (!user?.name)
```

### Use template literals instead of string concats

```typescript
// Not Preferred
const welcome = "Welcome " + user.name + " " + user.lastName + "!";

// Preferred
const welcome = `Welcome ${user.name} ${user.lastName}!`;
```

### Always use named exports

For simplicity and easy findings, export named variables instead of default exports.

```typescript
// Not Preferred
export default MyComponent;

// Preferred
export { MyComponent };
```

### Return early whenever possible

In order to have cleaner and more understandable code, and also to avoid running unnecessary logic, try returning early (with a “guard condition”) if certain conditions happen.

```typescript
// Not Preferred
const formatUser = (user) => {
 const fullName = user ? getFullName(user) : '';
 const age = user ? getAge(user.birthDate) : '';
 const formattedUser = user ? `${fullName}, age: ${age}` : '';

 return formattedUser;
}

// Preferred
const formatUser = (user) => {
 if (!user) return '';
 ...
}
```

### Use declarative programming over imperative

Prefer [`map`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map), [`filter`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) and [`reduce`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) to manipulate arrays instead of `for` loops or `forEach` method in situations where you want to perform specific operations over the iterated list.

```typescript
// Not Preferred
const admins = [];

users.forEach((user) => {
  if (user.type === "ADMIN") admins.push(user);
});

// Preferred
const admins = users.filter((user) => user.type === "ADMIN");
```

### Try to avoid mutations as much as possible

Mutations make code difficult to understand, test and debug. Changing an already declared variable may lead to unintended side effects in other parts of the code.

_Never mutate or re-assign function parameters._

```typescript
// Not Preferred
user.admin = true;
user.type = "ADMIN";

// Preferred
const admin = { ...user, admin: true, type: "ADMIN" };
```

```typescript
// Not Preferred
const makeUserAdmin = (user, options) => {
 if (!options) options = {};

 user.admin = true;
 ...
}

// Preferred
const makeUserAdmin = (user, options = {}) => {
 const adminUser = { ...user, admin: true };
}
```

### Prefer object literals over `switch` statements

Using object literals instead of `switch` statements will make the code more readable and performant. A switch has to evaluate each case until it hits a match and a break. Object literals are more efficient as they are a direct hash table lookup.

```typescript
// Not preferred
const getShiftColor = (shiftName) => {
  switch (shiftName) {
    case "am":
      return lightBlue;
    case "pm":
      return blue;
    case "noc":
      return darkBlue;
  }
};

// Preferred
const SHIFT_COLORS: {
  am: lightBlue;
  pm: blue;
  noc: darkBlue;
};

const getShiftColor = (shiftName) => SHIFT_COLORS[shiftName];
```

### Use ternary expressions to initialize variables

Instead of declaring a variable with `let` , initializing it, and then using an `if` statement to change its value, use a one liner ternary expression to initialize it.

```typescript
// Not Preferred
let tagText = text;

if (!expanded) {
  tagText = text.substring(0, 5);
}

// Preferred
const tagText = expanded ? text : text.substring(0, 5);
```

### Control flow | If vs ternary

Ternaries may seem like the superior, more concise version of an if statement on the surface. However, they both have equal value when you consider what they emphasize.

**If** statements emphasize branching logic first and what's to be done is secondary.

**Ternary** expressions emphasize what's to be done first and the selection of the values to do it with secondary.

In different situations, either may better reflect your "natural" perspective on the code and make it easier for others to understand, verify and maintain.

When deciding whether to use an if or a ternary ask yourself:

> "Should I emphasize that my code is branching, or should I emphasize what my code is doing?"

For example, if the thing you want to **_DO_** is assign a variable value based on a simple state, use a ternary:

```typescript
const isOpen: boolean = false;
const action: "notShowing" | "showing"; // value is `notShowing` by default because it's first in the union annotation

// Not preferred
if (isOpen) {
  action = "showing";
} else {
  action = "notShowing";
}

// Preferred
action = isOpen ? "showing" : "notShowing";
```

If the main takeaway of the next few lines of code you're about to write is about **_BRANCHING_** logic, use an if statement:

```typescript
const isAuthorized: boolean = true;

// Preferred
if (isAuthorized) {
  sendToDashboard();
} else {
  displayAlert();
  sendToOnboarding();
}

// Not preferred
isAuthorized ? doAuthedLogic() : doNotAuthedLogic();
```

As you can see, the ternary example not only works, but it does so very concisely. This is extremely subjective, but remember that emphasis is another tool we can leverage to support our colleague's ability to understand the code that we write faster and easier. Again, because in this example, our logic branches based on whether our user is authenticated, this pivotal point in our code is **_emphasized_** by the if statement. Being a fresh set of eyes reading through this code, you can very quickly understand that this is an important fork in our code flow.

With that being said, if your branching logic consists of more than just a boolean conditional and/or you expect the number of conditions to increase over time, you may want to consider using a switch statement or even more preferrably, an object literal if possible. This is why you'll find that ternary expressions, switch statements, and object literals are much more common throughout the codebase than if statements.

### Handle errors gracefully and notify the user with meaningful messages

```typescript
// Not Preferred
const createUser = async (email, password) {
 try {
   await api.createUser(email, password);
   toast('Success');
 } catch (error) {
   console.log(error);
   toast(error);
 }
}

// Preferred
const createUser = async (email, password) {
 try {
   await api.createUser(email, password);
   toast('Successfully created user');
 } catch (error) {
   console.log(error);
   toast('Failed to create user, please try again');
 }
}
```

### Always export inline at the component declaration

```typescript
// Preferred
export const Component = () => { ... }

// Not Preferred
const Component = () => { ... }

export { Component };
```

### Destructuring

#### Early vs late prop destructuring

For the same reason we declare our dependency imports at the top of a file, we should destructure all needed values from an object, array or tuple at the beginning of the body of a function. This will benefit a reader's ability to understand up front what data will be used and help to prevent overlooked values scattered throughout the rest of the logic of the function.

#### Destructuring method preferences

When possible, prefer to avoid dot notation when accessing values in objects, we can achieve this via destructuring in a single consice way no matter how nested the object is:

```typescript
const nestedObj = {
  layer1: {
    layer2: {
      layer3: {
        desiredValue: "digDeep!",
      },
    },
  },
};

// Preferred
const {
  layer1: {
    layer2: {
      layer3: { desiredValue },
    },
  },
} = nestedObj;

console.log(desiredValue); // "digDeep!"

// Not preferred
console.log(nestedObj.layer1.layer2.layer3.desiredValue);
```

### Custom hooks

Separating the UI from logic is almost always a good strategy, it's strongly preferred that we adbstract our logic into custom hooks to reduce file size, simplify testing and refactoring, and promote code reusability. When hooks become large (size TBD) they need to be broken into smaller, more maintainable hooks, just like any other component.

- #### Focus on reusability when building custom hooks

* When making a custom hook, prioritize designing your hook to be generalized enough to be used elsewhere in the codebase. If your hook can be used elsewhere, add it the the `/src/hooks` directory. For hooks that are highly specific for a particular task i.e: Extracting component-specific logic, put your hook into a `./hooks` directory at the root of the parent directory the hook is being used.

### Returning | When and when not to do so implicitly

If a function you're writing needs no declarations and can return its desired output without resorting to long one-liner expressions in order to be able to return implicitly, it's recommended you return implicitly.

On the other hand, if the logic of your function requires multiple logical steps or data massaging, avoid chaining methods together in a hard to read format simply to achieve implicit returns, use an explicit return instead.

```typescript
// Preferred
// Not sexy, but clear and easy to read
const makeFirstLetterCapitalExplicitly = (input: string): string => {
  const allButFirstCharLower = input.subString(1).toLowerCase();
  const firstCharCap = input.charAt(0).toUpperCase();
  const cappedWord = `${firstCharCap}${allButFirstCharLower}`;

  return cappedWord;
};

// Not preferred
const makeFirstCapitalLetterImplicitly = (input: string): string =>
  input.charAt(0).toUpperCase().concat("", input.substring(1));
```

While the implicit example is certainly more concise, conciseness is not always king, as overly consise statements like these can be mentally tiring to parse through and are prone to bug introduction.

### Function components | Use explicit returns

It's strongly recommended to use explicit returns for all function components, even if the component has no declarations. Often, components are refactored to have declarations and thus require being converted to an explicit return. By making all components return explicitly from the start will add consistency through the codebase and prevent large, difficult to read diffs at review time as a result of converting them from implicitly returned ones.

### Control flow | Conditional rendering

## Typescript Guidelines

#### Prop types naming standard

For naming prop types, use the name of the function receiving the props followed by 'Props' in PascalCase:

```typescript
// Preferred
type MyComponentProps = {}

// Not preferred
type myComponentProps = {}
```

#### Return value type naming standard

For naming function return types, use the name of the function followed by 'Return' in PascalCase:

```typescript
// Preferred
types MyComponentReturn {}

// Not preferred
types myComponentReturn {}
```

### Prop annotation

When annotating arguments for functions whether they are components, hooks, pure functions, etc, create a prop type. This holds true for return type annotations as well.

```typescript
// Preferred
type MyComponentProps = {
 prop1: string;
 prop2: boolean;
 prop3: number;
}

export const MyComponent: FunctionComponent<MyComponentProps> = ({
    prop1,
    prop2,
    prop3,
  }) => {
  ...
}

// Not preferred
export const MyComponent: FunctionComponent = ({
    prop1,
    prop2,
    prop3,
  }: {
    prop1: string,
    prop2: boolean,
    prop3: number,
  }) => {
  ...
}
```