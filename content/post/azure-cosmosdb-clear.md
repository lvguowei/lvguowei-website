+++
categories = ["Azure"]
description = "Clear Azure CosmosDB documents using NodeJS"
keywords = ["Azure", "CosmosDB", "clear cosmosdb documents", "delete cosmosdb documents"]
featured = "cosmosdb.png"
featuredpath = "/img"
title = "Clear Azure CosmosDB Documents"
date = 2018-07-26T11:02:46+03:00
+++


It suprised me that one cannot clear all documents in a CosmosDB collection from the web portal.

The only solution for now is to use its SDK, so I wrote a simple Node script.

It now can list all the documents and delete them, it can be easily modified to suit your need.

{{< highlight javascript >}}

var docdb = require("documentdb");
var async = require("async");

var config = {
  host: "https://xxxx.documents.azure.com:443/",
  auth: {
    masterKey: "xxxx"
  }
};

var client = new docdb.DocumentClient(config.host, config.auth);

var messagesLink = docdb.UriFactory.createDocumentCollectionUri("xxxx", "xxxx");

var listAll = function(callback) {
  var spec = {
    query: "SELECT * FROM c",
    parameters: []
  };

  client.queryDocuments(messagesLink, spec).toArray((err, results) => {
    callback(err, results);
  });
};

var deleteAll = function() {
  listAll((err, results) => {
    if (err) {
      console.log(err);
    } else {
      async.forEach(results, (message, next) => {
        client.deleteDocument(message._self, err => {
          if (err) {
            console.log(err);
            next(err);
          } else {
            next();
          }
        });
      });
    }
  });
};

var task = process.argv[2];
switch (task) {
  case "listAll":
    listAll((err, results) => {
      if (err) {
        console.error(err);
      } else {
        console.log(results);
      }
    });
    break;
  case "deleteAll":
    deleteAll();
    break;

  default:
    console.log("Commands:");
    console.log("listAll deleteAll");
    break;
}

{{< /highlight >}}
