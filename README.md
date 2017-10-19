# TinderApp - Messages Service

Analysis of a user's experience through their sent messages

## Roadmap

View the project roadmap [here](https://drive.google.com/open?id=1kAPJHYxOglYTeN3WJslR1_gGNFUneNer6oveAjPyoFA)

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for contribution guidelines.

# Table of Contents

1. [API Usage](#api-usage)
    1. [Input](#input)
    1. [Output](#output)
1. [Requirements](#requirements)
1. [Development](#development)
    1. [Installing Dependencies](#installing-dependencies)
    1. [Tasks](#tasks)

## API Usage

### Input

Messages expects message objects from a user.

A match queue is not expected because by the time messages are being exchanged, there is already a swipe match between the two users.

The idea is to examine the messages and put the quality of interaction on a quantifiable scale.

```javascript
{
  fromId: NUMBER,
  toId: NUMBER,
  message: STRING
}
```

- `fromId` ID of the user sending the message
- `toId` ID of the user receiving the message
- `message` Message Contents

#### Generated

- `messageID` ID of the message itself for databasing purposes
- `timeSent` Timestamp for tracking data

### Output

#### Queries

Queries can specify a time period to observe, or a certain user

```javascript
{
  userId: NUMBER,
  matchId: NUMBER,
  startDate: DATE,
  endDate: DATE
}
```

- `userId` _REQUIRED_ .The ID of the user to look up
- `matchId` _OPTIONAL_. The ID of the match to look up. Return all matches if blank
- `startDate` _OPTIONAL_. Beginning date of records to pull for user. Queries from first message if blank
- `endDate` _OPTIONAL_. End date of all records to pull for user. Queries to last message if blank

#### Response

Messages outputs analytics on the messages from a user to a user

```javascript
{
  averageMatchQuality: NUMBER,
  matchQualityTable: ARRAY,
  avgResponseTime: NUMBER,
  avgResponseLength: NUMBER,
  dateArranged: ARRAY,
  userActivity: ARRAY
}
```

- `averageMatchQuality` Average value of response quality. Defaults to 0 if no data
- `matchQualityTable` Array of score for each match the user messaged
- `avgResponseTime` Average time user takes to respond to a message
- `avgResponseLength` Average length of the responses sent by user
- `dateArranged` Array of users where a date was likely arranged (e.g. personal contact info was provided)
- `userActivity` Number of messages sent in the time period queried

## Requirements

- Node 6.9.x
- Postgresql 9.6.x
- express
- pg
- socket.io
- chai
- mocha
- faker

## Development
### Installing Dependencies
Run `npm install`

### Tasks

#### Quality Analysis

Quality analysis is determined by the following factors

Lower Quality | ⇐ Interaction ⇒ | Higher Quality
--- | :---: | ---:
Long | Response Time | Short
Negative | Response Keywords | Positive
None | Personal Information | Provided
Shorter | Response Length | Longer

#### Schema

![alt text](https://github.com/Jkwok0714/Thesis-Messages/blob/master/resources/schema.png "Schema 1")

#### Simulation

Traits for users are generated based on seed (user name) as to remain consistent throughout interactions with other users
- Every user has their own average response time and response lengths
- Every user has their preferred traits which will determine their interest level

1. Two users begin a session
1. Compatibility score is determined between two said users based on their bio traits
1. Compatibility score is used to simulate interest levels
1. Users send replies based on their interest level towards the match
    - If match is not interested, user stops replying after a certain number of tries
    - If user is interested, and match score between the two high enough, a date is arranged
5. Session ends

#### Movement of Data in System

- AWS SQS message buses
- Component expects user information from the Inventory Services
- Component expects message quality requests from Analysis and Matchmaking

## Other Information

(TODO: fill this out with details about your project. Suggested ideas: architecture diagram, schema, and any other details from your app plan that sound interesting.)
