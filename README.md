# twitter-archive-reader

> Read data from classic Twitter archive and GDPR archive (not all features are supported)

This module helps to read data from Twitter archives.
Not all data is available, in particular in the case of the GDPR archive.

This module is developed in mind to treat classic and GDPR archives the same way:
All the data specific of the GDPR archive will be ignored (except direct messages).

## Getting started


## Usage

```ts
// You can create TwitterArchive with all supported 
// formats by JSZip's loadAsync() method 
// (see https://stuk.github.io/jszip/documentation/api_jszip/load_async.html).
const archive = new TwitterArchive(readFileSync('filename.zip'));

console.log("Reading archive...");
// You must wait for ZIP reading and archive object build
await archive.ready();

// List the 30 first tweets in the archive
archive.all.slice(0, 30)

// Get all the tweets sent between two dates
archive.between(new Date("2018-01-24"), new Date("2018-02-10"));

// Get all the tweets sent in one month
archive.month("1", "2018");

// Get the number of tweets stored in the archive
archive.length;

// Tweets archive does NOT provide search functions except date helpers:
// Because there is tons of fields in tweets and you can search in all of them.

// DMs archive DOES provide search function and sub-objecting.
// DMs archive is group-conversation ready.
if (archive.is_gdpr) {
  // List the conversations available in the archive (GDPR archive)
  archive.messages.all
    .map(e => `Conversation #${e.id} between #${[...e.participants].join(', #')}`)

  // List the 30 DMs of the first conversation
  archive.messages.all[0].all.slice(0, 30);

  const conversation = archive.messages.all[0];
  
  // Search for messages around a specific DM
  conversation.around("MESSAGE_ID", 30 /* Want 30 messages before and 30 messages after */);

  // Search for messages sent in a specific date
  conversation.between(new Date("2019-01-01"), new Date("2019-02-04"));

  // Search for messages between two specific DMs
  conversation.between("MESSAGE_ID", "MESSAGE_2_ID");
  
  // Search for messages having specific text (use a RegExp to validate)
  conversation.find(/Hello !/i);

  // Search for messages received by a specific user
  conversation.recipient("USER_ID");

  // Every truncature method [.find(), .between(), .month(), .sender(), .recipient()]
  // returns a sub-object (SubConversation) that have his own index and own methods.
  // This allow you to chain methods:
  conversation
    .find(/Hello/i)
    .between(new Date("2019-01-01"), new Date("2019-02-01"))
    .recipient(["MY_USER_1", "MY_USER_2"]);
}
```
