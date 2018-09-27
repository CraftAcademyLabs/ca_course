### Setup Sails.js app

First we have to install Sails.js globally on your computer

```
npm install sails -g
```

Run ```node -v``` in the terminal to se what node version you have.
If the version is lower then 9.11.1 you need to update (```brew update node```) the node version for this setup to work.

Once this is done it is time to scaffold the application.

```
$ sails new adress_book
```

When you get the choice to choose a template for your new Sails app you should pick the empty one. So type in “2” in the terminal and press enter.

Now you need to cd into the folder.

```
$ cd adress_book
```

We need to setup the database for the application, run this in the terminal:

```
$ npm install sails-postgresql --save
```
Now we have to configure the application to know what databse to use.
The config/datastores.js should look like this:

```javascript
module.exports.datastores = {
  default: {
    adapter: "sails-postgresql",
    url: "postgresql://postgres:postgres@localhost:5432/adress_book"
  }
};
```

The application is now lokking for a database named "adress_book", but at this point we dont have a database named that. So let create one!
In the terminal run this :

```
$ createdb adress_book
```

If you open up PostgresQL on your computer you can now see that database.