## Node Implementation

![node-creation](https://user-images.githubusercontent.com/22747985/36630991-25065c4e-1968-11e8-8fbd-ec008980ec72.png)

```js
class Node {
  constructor(data, next = null) {
    this.data = data
    this.next = next
  }
}
```

Note - The property next is a reference to the `next node along the chain`, not the one before it.

## Linked list implementation

![linked-list](https://user-images.githubusercontent.com/22747985/36631066-2d87d55e-1969-11e8-8944-36847f87c2a5.png)

Well start off with the constructor function of the linked list which is used to initialize our linked list. We create the linked list with _**no head node**_ associated with it, so we can call `new LinkedList()` there should be no nodes associated with it. It should have one property called _**head**_, which is going to be a reference to the first node of the linked list. By default `head` should be `null`. The linked list has very little hard data associated with it, all it really knows is the first node of the list. We'll have to add methods to the class which will i.e. allow us to access the first node and crawl through the linked list and work through it in some meaningful fashion.

#### InsertFirst method

```js
class LinkedList {
  constructor() {
    this.head = null
  }

  insertFirst(data) {
    this.head = new Node(data, this.head)
  }
}
```

The insertFirst method on our LinkedList class allows us to insert nodes in our list. We call the method with some data, create a new node based on the data and assign our head property to this node. The first node we create won't contain a reference to another node and therefore it's next property will equal null, which is what initialized our head property of our linked list with. From then on, every node we insert into our list will point it's next property to the next node, i.e. this.head.

#### Size method

```js
class LinkedList {
  constructor() {
    this.head = null
  }

  insertFirst(data) {
    this.head = new Node(data, this.head)
  }

  size() {
    let size = 0
    let node = this.head
    while (node) {
      size++
      node = node.next
    }

    return size
  }
}
```

We create two local variables size and node which is assigned the value of the property head of our LinkedList class. While a node exists we increment our size variable and reassign our node variable to the next property of our node. When we eventually reach the end of our list, the tail, it should assign our node variable to null and we then return the size of the linked list.

#### getFirst method

```js
class LinkedList {
  constructor() {
    this.head = null
  }

  insertFirst(data) {
    this.head = new Node(data, this.head)
  }

  size() {
    let size = 0
    let node = this.head
    while (node) {
      size++
      node = node.next
    }

    return size
  }

  getFirst() {
    return this.head
  }
}
```

The getFirst method should return the first node of the linked list. Since we know that a linked list class has a property called `head` which is equal to the first node of our linked list, we can simply return that.

### getLast method

```js
  getLast() {
    if (!this.head) return null //premature optimization??

    let node = this.head

    while (node && node.next) {
      node = node.next
    }

    return node
  }
```

### clear method

```js
clear() {
  this.head = null
}
```

All we have to do to clear the list of any nodes is assign the head property to null as this is a reference to the first node of the linked list.

### removeFirst method

```js
  removeFirst() {
    if (!this.head) return
    this.head = this.head.next
  }
```

Removing the first node of the list involves assigning the head (which holds a reference to the first node of the list) to the head's next property which points to the next/second node in the list. In the case that there are no nodes in the list we simply return and do nothing.

### removeLast method

```js
getLast() {
  //if the list is empty, we immediately return
  if (!this.head) return
  //if the list only has one node, we don't want to do any unnecessary assignments/iterations
  if (!this.head.next) {
    this.head = null
    return
  }

  let previous = this.head
  let node = this.head.next

  while(node.next) {
    previous = previous.next
    node = previous.next
  }

  previous.node = null
}
```

### insertLast method

```js
insertLast(data) {
  const last = this.getLast() //First we retrieve the last node in our list

  if (last) {
    //If there is a node we'll set the next property on that node equal to our new node
    last.next = new Node(data)
  } else {
    //If the list is empty we'll set the head equal to our new node as it's the first node in our linked list
    this.head = new Node(data)
  }
}
```

### getAt method

```js
  getAt(index) {

    // if (index > this.size() || index < 0) return null
    //We don't want to call this.size(), because if index is not greater than this.size()
    //Then we're basically iterating through our linked list twice.

    // if (!this.head) return null
    // if node is null we're returning it at the bottom anyway, so the if statement is redundant

    let counter = 0
    let node = this.head

    while (node) {
      if (counter === index) return node
      counter++
      node = node.next
    }

    return null
  }
```

### removeAt method

```js
  removeAt(index) {
    if (!this.head) return

    if (index === 0) {
      this.head = this.head.next
      return
    }

//We're reusing our getAt method to retrieve the node before the given index.
// If that node doesn't exist or it's next property is null, we return out of the function.

// If not we set the previous node equal to the .next.next property.

    const previous = this.getAt(index - 1)
    if (!previous || !previous.next) return

    const nextNode = previous.next

    previous.next = nextNode.next
  }
```

### insertAt Method

```js
  insertAt(data, index) {
    // If the list is empty or the index is 0, we set the head equal to a new node with the data and it's next property pointing to the current head. So if list is empty it'll be null, if not it'll point to the first node in the list.
    if (!this.head || index === 0) this.head = new Node(data, this.head)

// We get the previous node before the provided index. If the index is out of bounds, we get the last node in the list. We then create the new node with it's next property equal to the previous one's.next's property! So we basically come in between two nodes.
    const previous = this.getAt(index - 1) || this.getLast(index - 1)

    const newNode = new Node(data, previous.next)
    previous.next = newNode
  }
```

## Code Reusability

If you look at the getFirst, getLast and getAt methods for example. You can see that the getFirst and getLast methods are a bit redundant, because our getAt method has the functionality to return a node at any given index, so we could rewrite these functions like so:

```js
getLast(){
  return this.getAt(this.size() - 1)
}

getFirst() {
  return this.getAt(0)
}
```

The key takeaway of this is that you should try and look ahead and if you for example start by implementing a getFirst or geLast method, maybe think about the future as you may need a generic getAt method which will cover both these methods too. Code reusability ftw!

### forEach method

```js
  forEach(fn) {
    let node = this.head
    let index = 0

    while (node) {
      fn(node, index)
      index++
      node = node.next
    }

    //recursive answer
    function modifyNode(node) {
      if (!node) return

      fn(node)
      modifyNode(node.next)
    }

    modifyNode(this.head)
  }
```

### Iterators and Generators

An object is an **_Iterator_** when it knows how to access items from a collection one a time, while keeping track of its current position within that sequence. In JavaScript an iterator is an object that provides `next()` method which returns the next item in the sequence. This method returns an object with two properties, namely: `done` and `value`.

An iterator is considered to be finished when `next()` returns an object with the `done` property set to `true`.

Example of an iterator:

```js
function makeIterator(array) {
  let nextIndex = 0

  return {
    next: function() {
      return nextIndex < array.length
        ? { value: array[nextIndex++], done: false }
        : { done: true }
    },
  }
}
```

While custom iterators are useful, it can be quite complicated to create them as they need to explicitly maintain their internal state. In come _**Generators**_.
Generators provide a powerful alternative as they allow you to define an iterative algorithm by writing a single function which can maintain its own state.

A GeneratorFunction is a special type of function that works as a factory (_an object for creating other objects_) for iterators. When it's executed it returns a new Generator object. A function becomes a GeneratorFunction if it uses the `function*` syntax.

Example:

```js
function* idMaker() {
  var index = 0
  while (true) yield index++
}

const generator = idMaker()

console.log(generator.next().value) // 0
console.log(generator.next().value) // 1
console.log(generator.next().value) // 2
```

You can also nest generators:

```js
function* numbers() {
  yield 1
  yield 2
  yield* moreNumbers()
  yield 6
  yield 7
}

function* moreNumbers() {
  yield 3
  yield 4
  yield 5
}

const generator = numbers()

for (let value of generator) {
  console.log(value) // 1, 2, 4, 5, 6, 7
}
```

Tree traversal example:

```js
class Tree {
  constructor(value = null, children = []) {
    this.value = value
    this.children = children
  }

  *printValues() {
    yield this.value
    for (let child of this.children) {
      yield* child.printValues()
    }
  }
}

const tree = new Tree(1, [
  new Tree(2, [new Tree(4)]), 
  new Tree(3)
])

const gen = tree.printValues()

console.log(
  gen.next(), // 1
  gen.next(), // 2
  gen.next(), // 4
  gen.next(), // 3
  gen.next() // done
)
```

If we call `printValues` on a single node of the tree, we will first yield that node's value. The next time we call next on the generator object, we will enter the for loop and we can delegate to other GeneratorFunctions. So for each child of the head node its printValues GeneratorFunction is called where first it's value is yielded and then subsequently its children's printValues will be called. So we get 1, 2, 4 and 3 from the example above.


### Making an object iterable :mind-blown:

An object is iterable if it defines its iteration behavior, such as what values are looped over in a `for...of` construct. Some built-in types such as `Array` or `Map` have a default iteration behaviour, while others, like objects do not. 

In order to be `iterable`, an object must implement the _**@@iterator**_ method. All this means is that the object must have a property with a `Symbol.iterator` key. This function should return a new iterator for each call, however this is not a requirement. 

Example: 

```js
const myIterable = {};
myIterable[Symbol.iterator] = function* () {
    yield 1;
    yield 2;
    yield 3;
};


//Iterable linked list example
  *[Symbol.iterator]() {
    let node = this.head
    while (node) {
      yield node
      node = node.next
    }
  }
```

Computed property names allows us to put an expression in brackets `[]` which will be computed and used as the property name. Both the Object initializer syntax and the class syntax support this feature.
