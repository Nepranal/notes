# Document Model

The document model is an alternative to the relational model. It's goals is to group information together and have operations performed per group and in effect have this sorts of tree structure.

![alt text](<sources/Relational Model Versus Document Model/Screenshot 2026-01-09 at 5.17.12 PM.png>)

More effectively, you can see it in this JSON form

```json
{
  "user_id": 251,
  "first_name": "Bill",
  "last_name": "Gates",
  "summary": "Co-chair of the Bill & Melinda Gates... Active blogger.",
  "region_id": "us:91",
  "industry_id": 131,
  "photo_url": "/p/7/000/253/05b/308dd6e.jpg",
  "positions": [
    {
      "job_title": "Co-chair",
      "organization": "Bill & Melinda Gates Foundation"
    },
    { "job_title": "Co-founder, Chairman", "organization": "Microsoft" }
  ],
  "education": [
    { "school_name": "Harvard University", "start": 1973, "end": 1975 },
    { "school_name": "Lakeside School, Seattle", "start": null, "end": null }
  ],
  "contact_info": {
    "blog": "http://thegatesnotes.com",
    "twitter": "http://twitter.com/BillGates"
  }
}
```

This type of models provide a great deal of flexibility since you don't have to specify a schema. And since you don't have to query multiple tables, you can enjoy a bit more efficiency.

Additionally, the document model and models similar to it, enjoy data locality where information that is pulled is close to one another. This property is quite difficult to assert with relational databases.

# Data Normalization

The issue with the document model is that it primarily supports (i.e. can perform really well) one-to-many relationships. As you move on to many-to-one or many-to-many relationship, you'll have to write code that will do this job for you to emulate join operations.

For instance, it's typical to have properties like `education` to be within it's own table and leave only references in the model that contains it. This sort of activity is called data normalization and can be very expensive for the document model.

And as it is, data tends to be more related and normalization becomes more and more attractive exposing the weakness of the document model.

# The Different Types of Database Models

As shown, the document model suffers as data becomes more related. At which stage, you may want to consider the relational model and define tables with foreign key on them. A little more work, but would be more efficient as you perform queries than to say implement it on code by yourself with the document model.

As it turns out however, relational model is not the end of the line. Your data can get even more related especially on the side of many-to-many relationship. With such a relationship, the relational model would define an extra table to extend record it. The more many-to-many you have, the more tables you need, and the more storage space you waste.

An alternative for highly related data is to use something called the network model. Each data point is modelled as a vertex and their relationship as an edge. Here's the kicker: between two vertices, you can define as many edges as you want and you can define each edge however you like. This way, no extra tables needed

# References

1. DDD Chapter 2
