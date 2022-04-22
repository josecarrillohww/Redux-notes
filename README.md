# Redux-notes
Redux is a state management library which follow the Flux arquitecture

## Terminology analogy
These are the basic terms of Redux using an analogy of an Insurance company.

![Screen Shot 2022-04-21 at 8 57 29 AM](https://user-images.githubusercontent.com/102980768/164486580-6ba10095-87f7-435c-b645-4d63e5537249.png)

## Actions
Using the same analogy, each kind of Form the Insurance company has, could be a Redux Action

![Screen Shot 2022-04-21 at 9 12 33 AM](https://user-images.githubusercontent.com/102980768/164489686-4c83c0e0-0d25-4eba-8358-e816969e273d.png)

All actions in redux have the same sintax, they return an object with type and payload

```Javascript
// This action take the name of the customer and the amount of money he pays for the policy
const createPolicy = ( name, amount ) => {
  return {
    type: 'CREATE_POLICY', // using All caps and dash separated for action types is a convention
    payload: {
      name: name,
      amount: amount
    }
  }
}

// This action only needs the name of the client
const deletePolicy = ( name ) => {
  return {
    type: 'DELETE_POLICY',
    payload: {
      name: name
    }
  }
}

// This action take the name of the customer and the amount of money the Insurance pays to the client
const createClaim = ( name, amountOfMoneyToCollect ) => {
  return {
    type: 'CREATE_CLAIM',
    payload: {
      name: name,
      amountOfMoneyToCollect: amountOfMoneyToCollect
    }
  }
}
```

## Reducers

Reducers are like the departments inside the Insurance company. We have another term which is the state, in this case it is the place where we put All Department data centralized, like the claims history, the policies and the accounting (money available). The departments receive the forms (actions) from the form receiver (dispatch) and do something with the data and finally update the state.

```javascript
// The claimsHistory reducer receives the list of claims, we name oldListOfClaims because it will be updated
// a default value of an empty array is provided as an initial value
// The second argument of any reducer always will be the action

const claimsHistory = ( oldListOfClaims = [], action) => {
    if (action.type === 'CREATE_CLAIM') {
      return [...oldListOfClaims, action.payload] // a brand new array will be created with the new values
    }
    
    return oldListOfClaims // If we don't care about the action
}

// The accounting reducer receives a bag of money with an initial value of 100 and the action
// if there is a createClaim action, this will substract money from the bag to pay the client
// if there is a createPolicy action, the money pay by the client will be added to the bag

const accounting = ( bagOfMoney = 100, action ) => {
  if (action.type === 'CREATE_CLAIM') {
    return bagOfMoney - action.payload.amountOfMoneyToCollect
  } else if (action.type === 'CREATE_POLICY') {
    return bagOfMoney + action.payload.amount
  }
  
  return bagOfMoney
}

const policies = ( listOfPolicies = [], action ) => {
  if (action.type === 'CREATE_POLICY') {
    return [...listOfPolicies, action.payload.name]
  } else if (action.type === 'DELETE_POLICY') {
    return listOfPolicies.filter(name => name !== action.payload.name) // this will return a new array without the item with the name we wat to delete
  }
  
  return listOfPolicies
}
```

## Redux in action

```javascript
  const { createStore, combineReducers } = Redux
  
  const ourDepartments = combineReducers({
    accounting: accounting,
    claimsHistory: claimsHistory,
    policies: policies
  })
  
  const store = createStore(ourDepartments)
  
  // In our analogy, dispatch is the guy who grab copies of the forms (actions) and take them to each department
  
  store.dispatch(createPolicy('Alex', 20)) // Alex pays $20
  store.dispatch(createPolicy('John', 30))
  store.dispatch(createPolicy('Bob', 25))
  
  // Now we have $175 in our money bag, remember we had $100 as initial state
  
  store.dispatch(createClaim('Alex', 120)  // Alex is claiming $120, so this amount will be substracted from money bag
  
  // Now we have $55 in our money bag
  
  store.dispatch(deletePolicy('Bob')) // We have the policy of the client named Bob
  
  // Finally, the state is like our document centralized repository, where we stored the data of claims, accounting and policies
  
  store.getState()
```
