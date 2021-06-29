# Configuring FAQ

The command to create and update FAQ commands is `importFAQ`. This command takes a .json file as a parameter. This file needs to abide to a certain schema.

## Schema
The enforced JSON schema for validation is available [here](https://raw.githubusercontent.com/Sheldan/OnePlusBot/master/application/oneplus-bot-modules/faq/src/main/resources/validation/createScheme.json)

## Explanation of the schema
This explanation tries to make a detailed explanation of the schema mentioned above, but is not guaranteed to be in sync with the actual schema used. In order to verify assumptions, please visit the schema location as this will be the schema which is actually enforced.

The general structure of the JSON file is an array of FAQ commands. Which looks like this:
```json
[
  {
    "faqCommandName": "test"
  },
  {
    "faqCommandName": "test2"
  }
]
```

There can be at most 30 FAQ commands in one JSON file. The maximum length of the FAQ command name is 30 characters and the only allowed characters are upper case, lower case, numbers and under score. The limit of FAQ command count only applies to a single import and not the total amount of FAQ commands available in the system.

Each command can have a list of aliases. There can be at most 20 aliases for each FAQ command, and the aliases have the same requirements as FAQ commands.

It is not allowed to define an alias which collides with another alias, or is the name of a to be configured or existing FAQ command.

Each FAQ command can have multiple responses, but each response has a respective channel group. There can be at most 255 responses for a FAQ command, but each FAQ command must have at least one response.
The channel group name has the same limitations as the FAQ command name.

Each message in a response _must_ have a position, in order to sort them correctly. There are at most 3 messages allowed, and the possible values for "position" are 0, 1 or 2. To phrase it differently, "position" defines the position of one individual message within the ordered messages.

The length of the text not within an embed and the description of an embed must be between 1 and 2000 characters.

The length of the imageURL used as an attachment must be between 1 and 2000. It must also start with `http`.
It is possible to determine the color of embeds with the `color` attribute of an embed. This is another object with three attributes (`red`, `blue`, `green`) respectively. The ranges for this values are 0-255.
This color object is optional, and if it is not defined the RGB values 0,0,0 will be used.

The embed also has a mandatory configuration object for the author which is in the attribute `author`. The name and the avatar of the configured ID will be shown in the resulting embed.
You can either define a userID to be used in the `userId` attribute, or define that the bot user should be used as author. This can be done by setting `useBot` to true. One of them must be set.

In general, the message must either contain a `additionalMessage` or an `embed` or both.

## Simple embed example
This channel group is referenced by name, and _must_ be of type `faq` in order to be used.
Below is an example of a FAQ command named `test`, with two aliases and a response for the channel group `test`.
The response has only one message which is an embed with only a description field containing the text `test description`.

```json
[
  {
    "faqCommandName": "test", 
    "aliases" : ["test2", "test3"],
    "responses": [
      {
        "channelGroupName": "test",
        "messages" : [
          {
            "position": 0,
            "embed": {
              "description": "test description",
              "author": {
                "useBot": true
              }
            }
          }
        ]
      }
    ]
  }
]
```

## Global responses
It is also possible to define a FAQ command as global. This means, that the command will be available in every channel of the server.
In order to do so, there is an attribute `global` on FAQ command level (the same level you define `faqCommandName`). This attribute must be set to `true`. There _must_ be only one response and this response must be for the channel group named `global`.

## General functionalities of the importFAQ command
If a FAQ command is not mentioned in the uploaded JSON file. It will not be affected by any changes.
In order to remove or update responses, you need to import the whole FAQ command, just with the removed sections. They will be removed.
In order to get the current configuration, you can use `exportFAQ <command>`. This will provide a valid configuration, but with more values than are required.
For example, it will not contain the `useBot` attribute, but rather the `userId` of the bot directly.
Also some optional values will have their values, for example `color`.
In order to delete a FAQ command use the `deleteFAQ` command with only the name as a parameter.

