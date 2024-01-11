# Sql-Indexed-Db
Simple to use Sql interface for IndexedDB

# Why
Most implementations are buggy and complicated and sql.js requires synchronous file api which is not possible without changing headers on server side. Exposing headers from server side isn't safe without understanding full consequences.

# State
Development - Not Ready for Production

# Design
This will contain simple API as shown below.

```javascript
// there is no way to upgrade database
// new one will be recreated in case of version mismatch
const db = await SqlIndexedDb.open("db", /* version */ 2);

const customers = await db.table.createTableIfNotExists("Customers", {
    customerID: {
        type: "bigint",
        generated: "identity"
    },
    firstName: {
        type: "string",
        max: 50
    },
    lastName: {
       type: "string",
       max: 50
    },
    address: {
       type: "string",
       nullable: true
    }
});

await customers.createIndex("IX_Customers_By_Name", {
    columns: {
        firstName: "ASC",
        lastName: "ASC"
    },
    filter: (x) => x.firstName || x.lastName
});

// create
const c = await customers.insert({
    firstName: "Akash",
    lastName: "Kava"
});

// retrieve
await customers
    .where({ id: 5 }, (p) => (x) => x.customerID === p.id)
    .first();

// delete
await customers
    .where({ id: 5 }, (p) => (x) => x.customerID === p.id)
    .delete();

// update
await customers
    .where({ id: 5 }, (p) => (x) => x.customerID === p.id)
    .update(void 0, (p) => (x) => ({ address: "no where" }));
```

