<h1>Introduction</h1>
<p>The project that these notes are written for uses the PERN stack along with typescript. These are some ideas that I had to thought over and over again before implementing them concretely. Some are nitpicks, some are genuine concerns as how to write and manage an application and deals with the arbitrariness within. It is within my beliefs that that to work harmoniously with others in building a system, one cannot write with constraints already in mind and trust others to follow it. Obviously you cannot get rid of constraints entirely, but these constraints should be 'natural' (whatever that means) else it benefits only one side of the parties involved and increases overall complexity that may not be necessary. Such a setup makes it difficult for others to contribute and adhere to the rules of the developed system. So, what I prefer is to force these constraints no matter what the people do.</p>

<h1>Representing Database Tables & Preserving Column Names </h1>
<p>One design that I particularly like is where the table column names, along with their types, becomes a trivial manner. The practice of just putting the names as literal and placing them wherever obviously will hurt if the column ever changes; a nice design that never even mention the under-lying names would be necessary. Conceptually, you can do this by assigning a kind of ID to the table name and it's column. Within PostgreSQL, you can define views to achieve this. Additionally, you can push this idea further, You can rename the tables with some ids and instead of referring the column names, you refer to them by their ordinal positions instead (i.e. The order they appear in for the table / column number).</p>

<p>There are some issues with this however. This scheme, while efficient in dealing with a changing database, which is common in the beginning stages of development, makes it difficult for others to come in and write with respect to it. This would only benefit a limited pool of people including the one who wrote the thing and whoever keeps changing the column names. Writing SQL scripts can be prone to more error and makes it even more difficult to assert for correctness.</p>

<p>A better approach seems to concretely define your data first and then develop incrementally. A database table typically corresponds to certain parts of the application that you're developing. This means that for that particular segment, makes sure your knowledge of the data to be processed is complete. Then, when new requirements come in (or moving to another segment of the application), you can create new tables or modify the original ones. Point is, having a constantly changing labels mean that your setup may be faulty. Something to assert here then is that a table and it's column will never change names. However, new tables and new columns may ne defined.</p>

<h1>Concrete Vs Partial Types And Untyped Values</h1>
<p>This relates to how data are being transferred from front-end to backend and vice versa. The problem here is how to represent as generic of an information as you can. I find it, within the context of a web application, quite usefull to transfer and return only partial values i.e. Records from a particular table with some columns missing. In case the data is actually missing (null, etc.) all functionalities, modules that are written already expect this and has already prepared default values. This can make changes to the architecture less destructive and easier to spot.</p>
<p>Additionally, since I'm using react and typescript, it may sound ridiculous, but dealing with the variable types can still be annoying to deal with. So, what I would do, since I'm not the one who wrote the modules to take in user input, is that I would not deal with type casting in the front-end but rather just leave it for the database to interpret it. In doing so, I can simply label the the payload to be sent as untyped (in typscript, you can do unknown) and just send it. If there's an error, I can simply set it up so the backend returns 4xx code back.</p>

<h1>Form Construction</h1>
<p>As it is common with any management application, receiving and organizing a big amount of data from a form is a crucial part. How can you possibly deal with such a thing? Well, it's obvioulsy not very nice to get the data from the form key-value pairs and just specify them to a key-value pair for the JSON. It's also, to my beliefs, not a good idea to make the columns in the database to match the keys used in the form since this can contaminate other applications that wants to use the same backend. Additionally, even if you do, you need to still somehow specify the table names anyways so it's a bit wasteful to spend that much just not to get to the finish line.</p>
<p>What you can do instead is to specify how that key would like to go in the database. I've done this in 2 ways: 1. Simply specify an array of strings for the particular key value pair where that array of string could be something like [table_name, column_name] 2. If your form is built from a json object, you can specify a factory method for your key that acts upon the payload you'd like to send. These functions will then be responsible to place in the value you care about.</p>

```typescript
// 2nd approach
status: {
    buildFn(record, value) {
        record["loan_particular_table"]["loan_status"] = value;
        return record;
    },
},
```

<p>The array idea can actually be made more concrete. You can define classes to do the job for you. In this example, I can define the classes and interfaces as such</p>

```typescript
// ------------------Interfaces------------------
export type AutopayFields = keyof AutopayTable;
export interface AutopayTable {
  // ...
}
interface Traceable<T extends string, K> {
  _path: string[];
  _currentPath: T;
  getPath(key: K): string[];
}

//------------------Classes----------------------
export class Autopay
  implements AutopayTable, Traceable<TableNames, AutopayFields>
{
  //...
  _currentPath: LmsTableNames;
  _path: string[];
  getPath(key: AutopayFields) {
    return this._path.concat([key]);
  }
}

```

```typescript
// 1st approach
{
    label: "Reference Number",
    type: "input",
    layout: "row-self-start",
    dbColumn: {
        "": autopay.getPath("reference_number"),
    },
},
```

<p>The 1st approach is actually my first attempt at cracking this problem. It works really well but it's not good if you want to preserve types. The methodologies of the 2nd approach I appreciate a little more. It's more flexible but you do have to write a little more. It really shines when you have to perform operations on the individual keys while keeping genericity</p>