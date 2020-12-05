# cucumber-jest
[![Build Passing](https://img.shields.io/badge/build-passing-green.svg)](https://github.com/Naereen/badges) [![Build Passing](https://img.shields.io/badge/dependencies-up_to_date-green.svg)](https://github.com/Naereen/badges)

A jest transformer for executing cucumber tests

```bash
npm i cucumber-jest -D
```

## Table of Contents

- [Gherkin Features](#gherkin-features)

- [Cucumber Features](#cucumber-features)

- [Getting Started](#getting-started)

    - [Jest Config](#jest-config)
        
    - [Example](#example)   
        - [Feature](#feature)
        - [Hooks](#hooks)
        - [Steps](#steps)
        - [World](#world)

- [Gherkin Variables \[new\]](#gherkin-variables)
   
## Gherkin Features

| Supported          | Feature                                                                                                                                                           | Notes                                                                      | 
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| :white_check_mark: | [And](https://cucumber.io/docs/gherkin/reference/#steps)                                                                                                          |                                                                            |
| :white_check_mark: | [Background](https://cucumber.io/docs/gherkin/reference/#background)                                                                                              |                                                                            |
| :white_check_mark: | [But](https://cucumber.io/docs/gherkin/reference/#steps)                                                                                                          |                                                                            |
| :white_check_mark: | [Comments](https://cucumber.io/docs/gherkin/reference/#descriptions)                                                                                              |                                                                            |
| :white_check_mark: | [Data Table](https://github.com/cucumber/cucumber-js/blob/master/docs/support_files/data_table_interface.md)                                                      |                                                                            |
| :white_check_mark: | [DocString](http://rmpestano.github.io/cukedoctor/cucumber-js/cucumber-js-documentation.html#_steps_accepting_a_docstring_parameter)                              | if it finds the docString is JSON, it will parse it for you             |
|                    | [Rule](https://cucumber.io/docs/gherkin/reference/#rule)                                                                                                          | haven't seen examples of this; not sure if it's worth it                   |
| :white_check_mark: | [Scenario](https://cucumber.io/docs/gherkin/reference/#descriptions)                                                                                              |                                                                            |
| :white_check_mark: | [Scenario Outline](http://rmpestano.github.io/cukedoctor/cucumber-js/cucumber-js-documentation.html#Scenario-Outlines-and-Examples)                               |                                                                            |


## Cucumber Features

| Supported          | Feature                                                                                                                                                           | Notes                                                                      | 
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| :white_check_mark: | [After](https://github.com/cucumber/cucumber-js/blob/master/docs/support_files/api_reference.md#afteroptions-fn)                                                  | called after each scenario in a feature file                               |
| :white_check_mark: | [AfterAll](https://github.com/cucumber/cucumber-js/blob/master/docs/support_files/api_reference.md#afteralloptions-fn)                                            | called after the feature file is completed; unlike Cucumber, you will have access to "this" context here.                                  |
|                    | [Attachments](https://github.com/cucumber/cucumber-js/blob/master/docs/support_files/attachments.md)                                                              |                                                                            |
| :white_check_mark: | [Before](https://github.com/cucumber/cucumber-js/blob/master/docs/support_files/api_reference.md#beforeoptions-fn)                                                | called before each scenario per feature file                               |
| :white_check_mark: | [BeforeAll](https://github.com/cucumber/cucumber-js/blob/master/docs/support_files/api_reference.md#beforealloptions-fn)                                          | called before the feature file is started; unlike Cucumber, you will have access to "this" context here.                                  |
| :white_check_mark: | [Given](https://github.com/cucumber/cucumber-js/blob/master/docs/support_files/api_reference.md#givenpattern-options-fn)                                          |                                                                            |
|                    | [setDefaultTimeout](https://github.com/cucumber/cucumber-js/blob/master/docs/support_files/api_reference.md#setdefaulttimeoutmilliseconds)                        | use jest.setTimeout or set the timeout property in your jest config        |
| :white_check_mark: | [setDefinitionFunctionWrapper](https://github.com/cucumber/cucumber-js/blob/master/docs/support_files/api_reference.md#setdefinitionfunctionwrapperwrapper)       |                                                                            |
| :white_check_mark: | [setWorldConstructor](https://github.com/cucumber/cucumber-js/blob/master/docs/support_files/api_reference.md#setworldconstructorconstructor)                     |                                                                            |
|                    | [Tags](https://github.com/cucumber/cucumber-js/blob/master/docs/cli.md#tags)                                                                                      | need to identify a way to pass tags through jest                           |
| :white_check_mark: | [Then](https://github.com/cucumber/cucumber-js/blob/master/docs/support_files/api_reference.md#thenpattern-options-fn)                                            |                                                                            |
| :white_check_mark: | [When](https://github.com/cucumber/cucumber-js/blob/master/docs/support_files/api_reference.md#whenpattern-options-fn)                                            |                                                                            |

## Getting Started

### Jest Config

You'll need to add the following to your jest config:

### moduleFileExtensions:
```json
{
  "moduleFileExtensions": [
    "feature", // <--- *1
    "js",
    "json",
    "ts",
    "tsx"
  ],
  "setupFilesAfterEnv": [
    "<rootDir>/node_modules/cucumber-jest/dist/init.js", // <--- *2
    "<rootDir>/test/world.ts",
    "<rootDir>/test/hooks.tsx",
    "<rootDir>/test/steps.ts"
  ],
  "transform": {
    "^.+\\.(js|jsx|ts|tsx)$": "babel-jest",
    "^.+\\.(feature)$": "jest-cucumber" // <--- *3
  },
  "testMatch": [
    "<rootDir>/path/to/your/*.feature" // <--- *4
  ],
  "testTimeout": 60000,
  "testURL": "http://127.0.0.1/",
  "verbose": true
}

```

1. Add ```feature``` to *moduleFileExtensions*
2. Add ```"<rootDir>/node_modules/cucumber-jest/dist/init.js"``` to *setupFilesAfterEnv*
   - this file calls cucumber's *supportCodeLibraryBuilder.reset* which sets up cucumber to start capturing registered hooks / steps
   - it's important to note that this file must be list first in the *setupFilesAfterEnv* list of files; before your world, hooks, or step files
3. Add ```"^.+\\.(feature)$": "jest-cucumber"``` as a *transformer*
4. Add ```"<rootDir>/path/to/your/*.feature"``` as a *testMatch* pattern

## Example

For a full featured example, please see the [example project](example)

### Feature

```path/to/your/features/button.feature```

```feature
Feature: Button

Given I go to home
When I click the login button
Then the login button is not visible
```

### Hooks

```path/to/your/hooks.tsx```

```typescript
import React from 'react';
import ReactDOM from 'react-dom';
import { act } from 'react-dom/test-utils'
import { AfterAll, BeforeAll } from 'cucumber';

import SignUp from './path/to/your/app';

BeforeAll(async function () {
    await act(async () => {
        ReactDOM.render(
            <SignUp/>,
            document.body
        )
    });
});

AfterAll(async function () {
    await act(async () => {
        ReactDOM.unmountComponentAtNode(
            document.body
        )
    });
});
```

You can choose to use the hooks to render/unmount your component before/after each feature file like above,
or you can add a path to your application entry point to your jest configuration's [setupFiles](https://jestjs.io/docs/en/configuration#setupfiles-array) property. 
The latter is more performant.
    
### Steps

```path/to/your/steps.ts```

```typescript
import { Given, When, Then } from 'cucumber';
import { act } from 'react-dom/test-utils';

Given(/I go to (.*)$/, function(link) {
    window.location.hash = `#/${link}`;
});

When(/I click the (\S+) button$/, async function(name) {
    await act(async () => {
        document.querySelector(`[data-test-id="${name}"]`).click();
    });
});

Then(/the (\S+) button is (visible|not visible)$/, function(name, state) {
    expect(!!document.querySelector(`[data-test-id="${name}"]`))
        .toEqual(state === 'visible')
});
```

### World

[setWorldConstuctor](example/test/world.ts) allows you to set the context of "this" for your steps/hooks definitions. 
This can be helpful when you want to maintain state between steps/hooks or want your steps/hooks to have access 
to some predefined data. The values are accessible within all Hooks, and Steps by using *this*

```path/to/your/world.ts```

```typescript
import { setWorldConstructor } from 'cucumber';

setWorldConstructor(
    class MyWorld {
        pages = [];
    }
);
```

### Output

Below is an example output from running tests against the [example project](example)
```text
 PASS  test/features/scenarioOutlineNested.feature (145 MB heap size)
  Feature: Sign Up - Scenario Outline [Nested] - Submitting With Extra Emails
    ✓ Given the firstName text input value is James (10 ms)
    ✓ And the lastName text input value is Dean (9 ms)
    ✓ And the email text input value is james.dean@gmail.com (10 ms)
    ✓ And the password text input value is itsASecretShh... (8 ms)
    ✓ And the extraEmails checkbox input is not checked (1 ms)
    ✓ When the submit button is clicked (90 ms)
    ✓ Then POST /api/sign-up is called with the request body:
       {
           "firstName": "James",
           "lastName": "Dean",
           "email": "james.dean@gmail.com",
           "password": "itsASecretShh...",
           "extraEmails": false,
           "date": "2019-12-01T15:00:00.000Z"
       } (3 ms)
    ✓ And the successAlert is visible (2 ms)
    ✓ And the showExtraEmailsAlert is not visible (3 ms)
  Feature: Sign Up - Scenario Outline [Nested] - Submitting Without Extra Emails
    ✓ Given the firstName text input value is James (10 ms)
    ✓ And the lastName text input value is Dean (8 ms)
    ✓ And the email text input value is james.dean@gmail.com (8 ms)
    ✓ And the password text input value is itsASecretShh... (9 ms)
    ✓ And the extraEmails checkbox input is checked (8 ms)
    ✓ When the submit button is clicked (40 ms)
    ✓ Then POST /api/sign-up is called with the request body:
       {
           "firstName": "James",
           "lastName": "Dean",
           "email": "james.dean@gmail.com",
           "password": "itsASecretShh...",
           "extraEmails": true,
           "date": "2019-12-01T15:00:00.000Z"
       }
    ✓ And the successAlert is visible (2 ms)
    ✓ And the showExtraEmailsAlert is visible (1 ms)

 PASS  test/features/scenarioBackground.feature (128 MB heap size)
  Feature: Sign Up - Without Extra Emails
    ✓ Given the firstName text input value is James (5 ms)
    ✓ And the lastName text input value is Dean (8 ms)
    ✓ And the email text input value is james.dean@gmail.com (9 ms)
    ✓ And the password text input value is itsASecretShh... (9 ms)
    ✓ When the submit button is clicked (50 ms)
    ✓ Then POST /api/sign-up is called with the request body:
       {
           "firstName": "James",
           "lastName": "Dean",
           "email": "james.dean@gmail.com",
           "password": "itsASecretShh...",
           "extraEmails": false,
           "date": "2019-12-01T15:00:00.000Z"
       } (1 ms)
    ✓ And the successAlert is visible (5 ms)
    ✓ And the showExtraEmailsAlert is not visible (2 ms)
  Feature: Sign Up - With Extra Emails
    ✓ Given the firstName text input value is James (9 ms)
    ✓ And the lastName text input value is Dean (10 ms)
    ✓ And the email text input value is james.dean@gmail.com (7 ms)
    ✓ And the password text input value is itsASecretShh... (8 ms)
    ✓ And the extraEmails checkbox input is checked (6 ms)
    ✓ When the submit button is clicked (36 ms)
    ✓ Then POST /api/sign-up is called with the request body:
       {
           "firstName": "James",
           "lastName": "Dean",
           "email": "james.dean@gmail.com",
           "password": "itsASecretShh...",
           "extraEmails": true,
           "date": "2019-12-01T15:00:00.000Z"
       }
    ✓ And the successAlert is visible (2 ms)
    ✓ And the showExtraEmailsAlert is visible (1 ms)

 PASS  test/features/scenarioOutline.feature (133 MB heap size)
  Feature: Sign Up - Submitting With Extra Emails
    ✓ Given the firstName text input value is James (5 ms)
    ✓ And the lastName text input value is Dean (7 ms)
    ✓ And the email text input value is james.dean@gmail.com (8 ms)
    ✓ And the password text input value is itsASecretShh... (10 ms)
    ✓ And the extraEmails checkbox input is not checked (1 ms)
    ✓ When the submit button is clicked (53 ms)
    ✓ Then POST /api/sign-up is called with the request body:
       {
           "firstName": "James",
           "lastName": "Dean",
           "email": "james.dean@gmail.com",
           "password": "itsASecretShh...",
           "extraEmails": false,
           "date": "2019-12-01T15:00:00.000Z"
       } (1 ms)
    ✓ And the successAlert is visible (1 ms)
    ✓ And the showExtraEmailsAlert is not visible (2 ms)
  Feature: Sign Up - Submitting Without Extra Emails
    ✓ Given the firstName text input value is James (9 ms)
    ✓ And the lastName text input value is Dean (9 ms)
    ✓ And the email text input value is james.dean@gmail.com (6 ms)
    ✓ And the password text input value is itsASecretShh... (7 ms)
    ✓ And the extraEmails checkbox input is checked (7 ms)
    ✓ When the submit button is clicked (40 ms)
    ✓ Then POST /api/sign-up is called with the request body:
       {
           "firstName": "James",
           "lastName": "Dean",
           "email": "james.dean@gmail.com",
           "password": "itsASecretShh...",
           "extraEmails": true,
           "date": "2019-12-01T15:00:00.000Z"
       } (1 ms)
    ✓ And the successAlert is visible (1 ms)
    ✓ And the showExtraEmailsAlert is visible (1 ms)

 PASS  test/features/scenario.feature (139 MB heap size)
  Feature: Sign Up - Without Extra Emails
    ✓ Given the firstName text input value is James (4 ms)
    ✓ And the lastName text input value is Dean (8 ms)
    ✓ And the email text input value is james.dean@gmail.com (8 ms)
    ✓ And the password text input value is itsASecretShh... (9 ms)
    ✓ When the submit button is clicked (48 ms)
    ✓ Then POST /api/sign-up is called with the request body:
       {
           "firstName": "James",
           "lastName": "Dean",
           "email": "james.dean@gmail.com",
           "password": "itsASecretShh...",
           "extraEmails": false,
           "date": "2019-12-01T15:00:00.000Z"
       }
    ✓ And the successAlert is visible (2 ms)
    ✓ And the showExtraEmailsAlert is not visible (2 ms)
  Feature: Sign Up - With Extra Emails
    ✓ Given the firstName text input value is James (8 ms)
    ✓ And the lastName text input value is Dean (8 ms)
    ✓ And the email text input value is james.dean@gmail.com (7 ms)
    ✓ And the password text input value is itsASecretShh... (8 ms)
    ✓ And the extraEmails checkbox input is checked (5 ms)
    ✓ When the submit button is clicked (35 ms)
    ✓ Then POST /api/sign-up is called with the request body:
       {
           "firstName": "James",
           "lastName": "Dean",
           "email": "james.dean@gmail.com",
           "password": "itsASecretShh...",
           "extraEmails": true,
           "date": "2019-12-01T15:00:00.000Z"
       } (1 ms)
    ✓ And the successAlert is visible (1 ms)
    ✓ And the showExtraEmailsAlert is visible (2 ms)

Test Suites: 4 passed, 4 total
Tests:       70 passed, 70 total
Snapshots:   0 total
Time:        10.142 s, estimated 24 s
Ran all test suites.
```


## Gherkin Variables

An additional feature of this library is **gherkin variables**. This feature allows you
to define data that you want to inject into the feature file before parsing it and executing the test.

### Use Without ENV

You can use this feature by setting up a file where the name matches the feature file you
want to use it with, and include **.vars** in the name, eg.

| Feature File            | Variable File               |  
| ----------------------- | --------------------------- | 
| scenarioOutline.feature | scenarioOutline.**vars**.js     |
| scenarioOutline.feature | scenarioOutline.**vars**.json   |
| scenarioOutline.feature | scenarioOutline.**vars**.ts     |

### Use With ENV
Alternatively, if you want to define multiple variable files for different environments,
you can set the **ENV** environment variable and include the value of **ENV** as part of the filename.

The table below contains multiple examples with different ENV values and extensions eg:

| Environment Variable    | Feature File            | Variable File                      |  
| ----------------------- | ----------------------- | ---------------------------------- | 
| ENV=**dev**             | scenarioOutline.feature | scenarioOutline.**dev**.vars.js    |
| ENV=**dev**             | scenarioOutline.feature | scenarioOutline.**dev**.vars.json  |
| ENV=**dev**             | scenarioOutline.feature | scenarioOutline.**dev**.vars.ts    |
| ENV=**qa**              | scenarioOutline.feature | scenarioOutline.**qa**.vars.js     |
| ENV=**qa**              | scenarioOutline.feature | scenarioOutline.**qa**.vars.json   |
| ENV=**qa**              | scenarioOutline.feature | scenarioOutline.**qa**.vars.ts     |
| ENV=**cert**            | scenarioOutline.feature | scenarioOutline.**cert**.vars.js   |
| ENV=**cert**            | scenarioOutline.feature | scenarioOutline.**cert**.vars.json |
| ENV=**cert**            | scenarioOutline.feature | scenarioOutline.**cert**.vars.ts   |

### Example Variable Use

The properties in your variable files can be used in your feature file by prefixing the json path with **$**, eg.

| Property Name In Feature File | Property Name In Variable File |
| ----------------------------- | ------------------------------ |
| $firstName                    | firstName                      |

** nested structures are also supported, please see examples below.

### Example With ENV & Flat Variables Structure

Testing Command:
```bash
ENV=dev $(npm bin)/jest
```

Variables File:
```typescript
// scenarioOutline.dev.vars.ts

export default {
    email: 'james.dean@gmail.com',
    firstName: 'James',
    lastName: 'Dean',
    password: 'itsASecretShh...'
};
```

Feature File:
```gherkin
# scenarioOutline.feature
  
Feature: Sign Up

  Scenario Outline: Submitting <prefix> Extra Emails
    Given the firstName text input value is $firstName
    And the lastName text input value is $lastName
    And the email text input value is $email
    And the password text input value is $password
    And the extraEmails checkbox input is <extraEmails>
    When the submit button is clicked
    Then POST /api/sign-up is called with the request body:
      """
       {
           "firstName": "$firstName",
           "lastName": "$lastName",
           "email": "$email",
           "password": "$password",
           "extraEmails": <extraEmailsValue>,
           "date": "2019-12-01T15:00:00.000Z"
       }
      """
    And the successAlert is <successAlert>
    And the showExtraEmailsAlert is <showExtraEmailsAlert>

    Examples:
      | prefix  | extraEmails | extraEmailsValue | successAlert | showExtraEmailsAlert |
      | With    | not checked | false            | visible      | not visible          |
      | Without | checked     | true             | visible      | visible              |
```

### Example With ENV & Nested Variables Structure

Testing Command:
```bash
ENV=qa $(npm bin)/jest
```

Variables File:
```typescript
// scenarioOutline.qa.vars.ts

export default {
    user: {
        email: 'james.dean@gmail.com',
        firstName: 'James',
        lastName: 'Dean',
        password: 'itsASecretShh...'
    }
};
```

Feature File:
```gherkin
# scenarioOutline.feature

Feature: Sign Up - Scenario Outline [Nested]

  Scenario Outline: Submitting <prefix> Extra Emails
    Given the firstName text input value is $user.firstName
    And the lastName text input value is $user.lastName
    And the email text input value is $user.email
    And the password text input value is $user.password
    And the extraEmails checkbox input is <extraEmails>
    When the submit button is clicked
    Then POST /api/sign-up is called with the request body:
      """
       {
           "firstName": "$user.firstName",
           "lastName": "$user.lastName",
           "email": "$user.email",
           "password": "$user.password",
           "extraEmails": <extraEmailsValue>,
           "date": "2019-12-01T15:00:00.000Z"
       }
      """
    And the successAlert is <successAlert>
    And the showExtraEmailsAlert is <showExtraEmailsAlert>

    Examples:
      | prefix  | extraEmails | extraEmailsValue | successAlert | showExtraEmailsAlert |
      | With    | not checked | false            | visible      | not visible          |
      | Without | checked     | true             | visible      | visible              |
```

** it's important to note that for nested structures you are using the path to access them, the same
way you would access a value from an object in javascript.

### Examples
| Type                         | Feature File                                                                 | Variable File                                                                                  |
| ---------------------------- | ---------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Variables **Without** Env    | [scenarioOutline](example/test/features/scenarioOutline.feature)             | [scenarioOutline.vars.ts](example/test/variables/scenarioOutline.vars.ts)                      |
| Variables **With Env** (dev) | [scenarioOutlineNested](example/test/features/scenarioOutlineNested.feature) | [scenarioOutlineNested.dev.vars.ts](example/test/variables/scenarioOutlineNested.dev.vars.ts)  |


### Variable File Rules

1 - must be located within your project

2 - uses an extension defined in your jest configuration: [**moduleFileExtensions**](https://jestjs.io/docs/en/configuration#modulefileextensions-arraystring)

2 - can be parsed into a javascript object, eg. .js, .json, .ts

