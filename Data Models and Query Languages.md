# Relational Model Vs Document Model

It started with relational model where data is organized by how they relate to one another. The main purpose of the relational database, and the reason it became popular, was to be used to develop application with rigid structure. The use cases, as you can imagine, are quite straightforward; include things like transaction and batch processing. As it turns out, the relational model generalize quite nicely (e.g. You can use it even if your data doesn't have relations).

In contrast to the relational model comes 'NoSQL'. The main thing being that we no longer have relations. And yes, you can combine the two into something called 'polyglot persistence'.

# Object Relational Mismatch

An issue with relational model is a practical one (maybe?). Usual applications are developed using OOP and in order to use results from our query, you'd usually have a translation layer that maps your results into an object. This is fine for the most part but becomes annoying when your query is just a table for which the structure you have already defined. This disconnect is actually quite bad because imagine if you want to change the db model; you'll have to change the stuff in the translation layer as well.

# Case Study: User Profile

Say we would like to develop a relational database for a user that displays their info (kinda like Linkedin, Facebook, etc.). One way is to do the following:

![alt text](<sources/Data Models and Query Languages/Screenshot 2026-02-19 at 9.48.52 PM.png>)

Here we see a fair bit of one-to-one and many-to-many relationships. For one-to-one, you can see `regions` and for many-to-many would be the `education` table.

For these sort of 'structured' information, at times you'd probably would like to represent it as such as well

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

If you're doing NoSQL, this would be very close to what you're actually storing; a document. These are the preferences of a NoSQL database.

The point to make here is that relational databases can indeed cover these more 'localised' data use cases but as you see they tend to be complicated. If we were to use a document-based database instead, the management and processes would be simpler.

However, application changes over time. As it turns out, usually, data becomes more relational and you would eventually revert back to a relational database model.

# The Network Model

Early database systems were following what's called the hierachical model. It works similarly as the document model; great at one-to-many relationships but does not support joins and so suffer when it comes to many-to-many (and basically other forms where we care about resolving references). From this model comes the relational model and one more: network model.

Within the network model, each record could have multiple parents as opposed to hierarchical which only allows 1. This in effect allows you to create a sort of linked list of records. For instance, say you have a record that stores an information about a particular region. From that record, to model many-to-many, we can attach a sort of pointer from that record to, say, everyone who lives in that region. This linked list can go on as long as possible. To access a record, we require an 'access path' which is basically the links. Doing this, you're kinda like doing joins during insert time

The issue with the network model is on how complicated and inflexible it can be since you have to manage the access paths yourself.

# When To Use What?

Document, relational, and network models have their own set of advantages. When to use which however depends heavily on your application. A rule of thumb is to rank the three that we have so far based on how 'connected' your data is:

- Document: Weakly connected
- Relational: Moderate
- Network: Strong

There are a lot of other things to consider as well. First is regarding the schema. This is actually a bit misleading as the db is always inferring some sort of structure. Relational DBs have what's called schema-on-write where we enforce the structure to be stored while others follow schema-on-read where the structure is inferred.

This consideration is mainly on when you're not sure on what the data would look like. If you use non-relational, then you can modify the schema with little trouble. Say you'd like to add a column. Using a relational db, you'd have to add this to every single rows you have. While with the document-model, you don't have to. Just add the new field with incoming new data (though you'll have to write code to handle both new and old formats). In general, if you don't have control over the structure, you may want to consider the document model.

Second is on data locality. This is a little low level, but if your data always come in a package, then it would be nicer to store them close together in your disk storage. Document models do this by default. Though, it should be mentioned that relational models also support these type of indexing. However, data locality is usually only useful in cases where you want the entire data together. If you have to write, it may cause more harm than good

I think though, in general if you truly have no clue, then perhaps relational should be considered. Most relational databases now supports a JSON-like data type which effectively allows you to turn it into document model if you need so

# References

1. Kleppmann, M. (2017). Designing data-intensive applications. Sebastopol, CA: O’Reilly Media.
