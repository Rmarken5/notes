# Overview
## What is react

* Declarative
* Efficient
* Flexible

Lets you compose complex UIs from small isolated pieces of code called "components"

```typescript jsx
class ShoppingList extends React.Component {
  render() {
    return (
      <div className="shopping-list">
        <h1>Shopping List for {this.props.name}</h1>
        <ul>
          <li>Instagram</li>
          <li>WhatsApp</li>
          <li>Oculus</li>
        </ul>
      </div>
    );
  }
}

// Example usage: <ShoppingList name="Mark" />
```

A component takes parameters called props and returns a hierarchy of views to display via the render method.

the render method returns a _description_ of what you want to see.

React take the description and displays the result.

Render returns React element.

React Element - lightweight description of what to render.

Most React developers use special syntax called "JSX" which makes these structures easier to write.

JSX comes with the full power of JavaScript. You can putt _any_ JavaScript expressions within braces inside JSX.
Each React element is a JavaScript objet that you can store in a variable or pass around in your program.

The `ShoppingList` component above only renders built-in DOM  components like `<div />` and `<li />`. 
You can compose and render custom React components too.

## Passing Data Through Props

```typescript jsx
class Board extends React.Component {
  renderSquare(i) {
    return <Square value={i} />;
  }
}

```
```typescript jsx
class Square extends React.Component {
    render() {
        return (
            <button className="square">
                {this.props.value}
            </button>
        );
    }
}
```

## Making an Interactive Component

Add the onClick event handler by adding a function.
```typescript jsx
class Square extends React.Component {
  render() {
    return (
      <button className="square" onClick={function() { console.log('click'); }}>
        {this.props.value}
      </button>
    );
  }
}
```

Next is to add state to remember which button was clicked.


React components can have state by setting `this.state` in their constructors.
`this.state` should be considered as private to a React component that it's defined in. Let's store the current state value of the Square in `this.state`, and change it when Square is cliced.

Add constructor to the class to initialize state.

```typescript jsx
class Square extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: null,
    };
  }

  render() {
    return (
      <button className="square" onClick={() => console.log('click')}>
        {this.props.value}
      </button>
    );
  }
}
```
Add this.setState({value: 'X'}) to the click listener.

## Completing the Game

### Lifting up state
Need to maintain each of the 9 squares in one location so a winner can be determined.

__To collect data from multiple children, or to have two child components communicate with each other, you need to declare the shared state in their parent component instead. The parent component can pass the state back down to the children by using props; this keeps the child components in sync with each other and with the parent component.__

Add state to board constructor is initialized all elements as null.

```typescript jsx
class Board extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      squares: Array(9).fill(null),
    };
  }

  renderSquare(i) {
    return <Square value={i} />;
  }
```

Add click handler to Square element in board `renderSquare`

```typescript jsx
  return <Square 
              onClick={() => this.handleClick(i)}
             value={this.state.squares[i]}/>;
```

Add handle click function inside board so it can be passed.

```typescript jsx
 handleClick(i) {
    const squares = this.state.squares.slice();
    squares[i] = 'X';
    this.setState({squares: squares});
  }
```

Since the `Square` component is taking input from `Board`, it's considered __controlled__

### Why Immutability Is Important
In the previous code example, we suggested that you create a copy of the squares array using the slice() method instead of modifying the existing array. We’ll now discuss immutability and why immutability is important to learn.

There are generally two approaches to changing data. The first approach is to mutate the data by directly changing the data’s values. The second approach is to replace the data with a new copy which has the desired changes.

#### Complex Features Become Simple
Immutability makes complex features much easier to implement
 
#### Detecting Changes
Detecting changes in mutable object is difficult because they are modified directly.
This detection requires the mutable object to be compared to previous copies of itself and the entire object tree to be traversed.

Detecting changes in immutable objects is considerably easier. If the immutable object that is being referenced is different than the previous one, then the object has changed.

#### Determining When to Re-Render in React
The main benefit of immutability is that it helps you build _pure components_ in React.
Immutable data can easily determine if changes have been made, which helps to determine when a component requires re-rendering.

You can learn more about `shouldComponentUpdate()` and how you can build `pure components` by reading Optimizing Performance.

### Functional Components
In React, __functional components__ are a simpler way to write components that only contain `render` method and don't have their own state.
Instead of defining a class which extends `React.Component`, we can write a function that takes `props` as input and returns what should be rendered.

Function components are less tedious to write than classes, and many compnents can be expressed this way.

Making the Square component functional.

```typescript jsx
function Square(props) {
  return (
    <button className="square" onClick={props.onClick}>
      {props.value}
    </button>
  );
}
```

Calculating the winner

```typescript jsx
function Square(props){
  return (<button className="square" onClick={props.onClick}>
    {props.value}
  </button>);
}

class Board extends React.Component {
  constructor(props){
    super(props);
    this.state = {
      squares: Array(9).fill(null),
      xIsNext: true,
    }
  }
  
  
  handleClick(i){
    const squares = this.state.squares.slice();
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    squares[i] = this.state.xIsNext ? 'X' : 'O';
    this.setState({
      squares: squares,
      xIsNext: !this.state.xIsNext,
    });
  }
  renderSquare(i) {
    return <Square 
              onClick={() => this.handleClick(i)}
             value={this.state.squares[i]}/>;
  }

  render() {
    const winner = calculateWinner(this.state.squares);
    let status;
    if (winner) {
      status = 'Winner: ' + winner;
    } else {
      status = 'Next player: ' + (this.state.xIsNext ? 'X' : 'O');
    }

    return (
      <div>
        <div className="status">{status}</div>
        <div className="board-row">
          {this.renderSquare(0)}
          {this.renderSquare(1)}
          {this.renderSquare(2)}
        </div>
        <div className="board-row">
          {this.renderSquare(3)}
          {this.renderSquare(4)}
          {this.renderSquare(5)}
        </div>
        <div className="board-row">
          {this.renderSquare(6)}
          {this.renderSquare(7)}
          {this.renderSquare(8)}
        </div>
      </div>
    );
  }
}

class Game extends React.Component {
  render() {
    return (
      <div className="game">
        <div className="game-board">
          <Board />
        </div>
        <div className="game-info">
          <div>{/* status */}</div>
          <ol>{/* TODO */}</ol>
        </div>
      </div>
    );
  }
}

// ========================================

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<Game />);


function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}
```
