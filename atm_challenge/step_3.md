# Step 3

Okay, so we have a basic `withdraw` method for our Atm class. It's a good start. Now, if we have a look at the requirements we initially got from our client, we see that a successful withdraw should generate a response in the form of a Hash. 

**This hash is the equivalent of a receipt that the Atm prints out in the real life.** It should look like this if the transaction was successful:

```
{ status: true, message: 'success', date: '2016-01-30', amount: 35, bills: [20,10,5]}
```

For unsuccessful transactions, it should look like this:

```
{ status: false, message: '[reason for failure e. e. wrong pin]', date: '2016-01-30'}
```

Let's break this down. 

**`status`** 
- Can be `true` or `false` depending if the transaction was successful.

**`message`** 
- A message to the user. We can set that to `success` when the transaction was successful and to something else if we for some reason can not perform the transaction. 

**`date`**
- The date of the transaction - simply today's date.

**`amount`** 
- Visible only when transaction was successful. 
- Simply the amount that wes withdrawn.

**`bills`**
- Visible only when transaction was successful.
- An array of bills that was dispached by the Atm. This symbolize the actual cash you would get in real life. 


