---
title: "Decorator in Typescript" # Title of the blog post.
date: 2021-01-11T00:11:47+07:00 # Date of post creation.
description: "Descorator in Typescript." # Description used for search engine.
featured: true # Sets if post is a featured post, making appear on the home page side bar.
draft: false # Sets whether to render this page. Draft of true will not be rendered.
toc: false # Controls if a table of contents should be generated for first-level links automatically.
# menu: main
featureImage: "https://codetheworld.io/wp-content/uploads/2019/09/header-6.png" # Sets featured image on blog post.
thumbnail: "https://miro.medium.com/max/816/1*mn6bOs7s6Qbao15PMNRyOA.png" # Sets thumbnail image appearing inside card on homepage.
shareImage: "/images/path/share.png" # Designate a separate image for social media sharing.
codeMaxLines: 10 # Override global value for how many lines within a code block before auto-collapsing.
codeLineNumbers: false # Override global value for showing of line numbers within code block.
figurePositionShow: true # Override global value for showing the figure label.
math: true
series:
  - Typescript
categories:
  - Technology
tags:
  - typescript
# comment: false # Disable comment if false.
---
<!-- **Insert Lead paragraph here.** -->
*This article helps you to understand decorator, an experimental of typescript and may introduce breaking changes in future releases.*
# About Decorator
> A Decorator is a special kind of declaration that can be attached to a class declaration, method, accessor, property, or parameter. Decorators are functions that take their target as the argument. With decorators we can run arbitrary code around the target execution or even entirely replace the target with a new definition.  
# Prerequisites
- Javascript Fundamental
- Node.js
# Getting started
There're varios type of decorators in Typescript:
  - Class
  - Method
  - Accessor
  - Property
  - Parameter

This article will focus mainly on the decorator types: Class, Method, Property and Parameter.  
Since it's being the experimental feature, so we have to enable it by modify the `tsconfig.json`.  
```js
{
  /* Experimental Options */
  "experimentalDecorators": true, /* Enables experimental support for ES7 decorators. */
}
```
## Useful Concepts
### Decorator Factories
Decorator Factories is simply a function return another function which allow you to control the default behavior of your decorating target. 
eg: 
Apply decorator for a function
```typescript
function decorate(
  target: any,
  propertyKey: string,
  descriptor: TypedPropertyDescriptor<any>
) {
  // do sth
}

class Student {
  @decorate
  getName() {
    // do sth
  }
}
```

Instead of doing that, we can modify the `decorate` function as below:
```typescript
function decorate() {
  return function (
    target: any,
    propertyKey: string,
    descriptor: TypedPropertyDescriptor<any>
  ) {
    console.log('do sth');
  }
}

class Student {
  @decorate()
  getName() {
    // do sth
  }
}
```
{{% notice note %}}
Too many parameters in a function, right? Don't panic, I will explain the function that take 3 arguments: `target`, `propertyKey`, `descriptor` in the next session.
{{% /notice %}}

As you can see, when `decorator factories`, the usage of it also looks different.  
`@decorate` => `@decorate()`, as a result, we can be free to pass anything into the decorator and do more stuff in it.  

### Decorator Composite 
Decorateor composite aims to the order of your decorators will be evaluated and excecuted.  

As the above example, if we define two decorators such as: `decorateA` and `decorateB` and apply it in a method
```typescript
function decorateA() {
  console.log('decorateA() was evaluated');
  return function (
    target: any,
    propertyKey: string,
    descriptor: TypedPropertyDescriptor<any>
  ) {
    console.log('decorateA() got excecuted');
  }
}

function decorateB() {
  console.log('decorateB() was evaluated');
  return function (
    target: any,
    propertyKey: string,
    descriptor: TypedPropertyDescriptor<any>
  ) {
    console.log('decorateB() got excecuted');
  }
}

class Student {
    @decorateA()
    @decorateB()
    getName() {
      // do sth
    }
}

new Student().getName();
```

![Evaluation and Execution Order :: image-scalable](https://i.imgur.com/x2y8TxG.png)
It works similarly to [`Function Composition`](https://en.wikipedia.org/wiki/Function_composition_(computer_science)) in mathermatics.

```
(g ∘ f )(x) = g(f(x))
```

> The expressions for each decorator are evaluated top-to-bottom.  
The results are then called as functions from bottom-to-top.
## Class Decorator
A `Class Decorator` has the following characteritics:  
- Declared before class declaration.
- Applied to class constructor.
- Used to observer, modify or replace the class definition.

The expression receives only the class constructor as its argument.

eg:
```typescript
function welcome<T extends { new(...args: any[]): any }>(constructor: T) {
  return class extends constructor {
    greeting: string = 'Welcome to Harvard university';
  }
}

@welcome
class Student {
  constructor(name: string) {

  }
}

const student = new Student("Nhat");
console.log(student);
```
![class decorator example :: image-scalable](https://i.imgur.com/Ov7xPtS.png)

## Method Decorator
A `Method Decorator` has the following characteritics:  
- Declared before method declaration.
- Applied to Object `Property Descriptor`.
- Used to observer, modify or replace the method definition.  

The expression will receives:
- Either the constructor function of the class for a static member, or the prototype of the class for an instance member.
- The name of the member.
- The Property Descriptor for the member.

eg:
```typescript
function enumarable(
  target: any,
  propertyKey: string,
  descriptor: TypedPropertyDescriptor<any>
) {
  descriptor.enumerable = true;
  return descriptor;
}

class Student {
  private _name: string;
  constructor(name: string) {
    this._name = name;
  }

  @enumarable
  getInfo() {
    // do sth
    return this._name;
  }
}

const student = new Student("Nhat");
```

## Property Decorator
A `Parameter Decorator` has the following characteritics:  
- Declared before property declaration. 
- Used to observer, modify or replace the class definition.  

The function expression will receives:
- Either the constructor function of the class for a static member, or the prototype of the class for an instance member.
- The name of the member.  

{{% notice info %}}
There's no currently `Property Descriptor` supported to property decorator.
{{% /notice %}}
## Parameter Decorator
A `Parameter Decorator` has the following characteritics:  
- Declared before parameter declaration.
- Applied to the function for a class constructor or method declaration. 
- Used to observer, modify or replace the class definition.

The function expression will receives:
- Either the constructor function of the class for a static member, or the prototype of the class for an instance member.
- The name of the member (method),  as its arguments.
- The ordinal index of the parameter in the function’s parameter list.
```typescript
const humanNameMetadataKey = Symbol('humanName');
function humanName(
  target: any,
  propertyKey: string,
  parameterIndex: number
) {
  const humanNameParameters: number[]
    = Reflect.getOwnMetadata(
      humanNameMetadataKey,
      target,
      propertyKey,
    ) || [];
  humanNameParameters.unshift(parameterIndex);
  Reflect.defineMetadata(
    humanNameMetadataKey,
    humanNameParameters,
    target,
    propertyKey,
  )
}

function validate(
  target: any,
  propertyKey: string,
  descriptor: TypedPropertyDescriptor<any>
) {
  const originalMethod = descriptor.value;
  descriptor.value = function (...args: any[]) {
    console.log('checking before returning');
    const humanNameParameters = Reflect.getOwnMetadata(
      humanNameMetadataKey,
      target,
      propertyKey,
    );

    if (humanNameParameters) {
      for (let humanNameParamIndex of humanNameParameters) {
        const value = arguments[humanNameParamIndex];
        if (!/^[a-zA-Z\s]*$/.test(value)) {
          throw new Error('Invalid human name.');
        }
      }
    }

    return originalMethod.apply(this, args);
  }
}
```

```typescript
class Student {
  private _name: string;
  constructor(name: string) {
    this._name = name;
  }

  @validate
  setName(@humanName name: string) {

    this._name = name;
  }

  getInfo() {
    return this._name
  }
}

const student = new Student("Nhat");
student.setName("Vincent Pham");
console.log(student.getInfo());
```

![Parameter decorator example ::center image-scalable](https://i.imgur.com/LtCrTmv.png)
# Conclusion
Decorator is powerful for modifying the class definition, athough it's still being the experimental feature, but hopefully, it will be adopted officially in the near feature.

Clone {{% button href="https://github.com/vincentpham13/typescript-master/tree/master/decorator" icon="fab fa-github" icon-position="left" %}}The completed demo code{{% /button %}} here