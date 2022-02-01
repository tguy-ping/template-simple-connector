# Connector Development Lab

This lab shows how to add the ability to Base64 encode text on top of an existing connector.

This lab requires an environment to be prepared with some expectations:

- Personal connector development environment built from https://davinci.pingidentity.cloud/
- Connector template from https://github.com/pingidentity-sk/template-simple-connector

Steps for the lab include:

- [Manifest.js](#manifest) - Add a capability definition.
- [Index.js](#index) - Add code to be called per definition on manifest.
- Update Flow to use the new capability

## Manifest

Open `/home/coder/myconnector/manifests/manifest.js` in your browser vscode.

> Some lines on manifest are very long, you can turn off word-wrap with option+z on mac (or via hamburger menu>view>word-wrap )

Make the following changes on myconnector/manifests/manifest.js

Copy this code block (all the way to left margin to maintain indentation)

```js
    /*
     Here we define the 'textToEncode' property, which is later declared as something that is
    assigned a value in the flow
     */
    textToEncode: {
      displayName: 'Text to Base64 Encode',
      info: 'Text to Base64 Encode',
      preferredControlType: 'textField',
      enableParameters: true,
    },
```

On manifest.js enter a new line after 184. On the newline, backspace to left margin and paste the code block.

Results should look like:

```js
      /*
       This field is displayed next to the input to describe what the value should be
       */
      info: 'The body of the POST call',
    },
    /*
     Here we define the 'textToEncode' property, which is later declared as something that is
    assigned a value in the flow
     */
    textToEncode: {
      displayName: 'Text to Base64 Encode',
      info: 'Text to Base64 Encode',
      preferredControlType: 'textField',
      enableParameters: true,
    },
  },
  /*
   the 'capabilities' object enumerates the capabilities that the orchestration
   engine can call and that the UI needs to render configuration panels for
   */
```

Next, copy this code block:

```js
    base64Encode: {
      type: 'action',
      title: 'Base64 Encode string',
      subTitle: 'Base64 Encode string',
      respondToUser: true,
      apiEnabled: true,
      inputs: [],
      flowConfigView: {
        items: [
          {
            propertyName: 'textToEncode',
          },
        ],
      },
      payloadInputSchema: {
        default: {
          type: 'object',
          properties: {
            properties: {
              type: 'object',
              properties: {
                textToEncode: {
                  type: 'string',
                  description: 'Text to Base64 Encode',
                },
              },
            },
          },
        },
      },
    },
```

On manifest.js enter a new line after 318. On the newline (319) backspace to left margin and paste the code block.

Results should look like:

```js
            statusCode: {
              type: 'number',
            },
          },
        },
      },
    },
    base64Encode: {
      type: 'action',
      title: 'Base64 Encode string',
      subTitle: 'Base64 Encode string',
      respondToUser: true,
      apiEnabled: true,
      inputs: [],
      flowConfigView: {
        items: [
          {
            propertyName: 'textToEncode',
          },
        ],
      },
      payloadInputSchema: {
        default: {
          type: 'object',
          properties: {
            properties: {
              type: 'object',
              properties: {
                textToEncode: {
                  type: 'string',
                  description: 'Text to Base64 Encode',
                },
              },
            },
          },
        },
      },
    },
  },
  /*
    the 'accountConfigView' object stores an 'items' array of the properties
    (as defined earlier) to display at the connection level.
    In other words, these are the fields that are share by every flow
    using the connection in the company.
   */
```

## Index

Make the following changes to `myconnector/index.js`

Copy this code block:

```js
const handle_capability_base64Encode = async ({ properties }) => {
  logger.info('overriding handle_capability_base64Encode');
  try {
    console.log(properties);
    const { textToEncode } = properties;
    const response = Buffer.from(textToEncode).toString('base64');
    console.log(`encoded response is: ${response}`);
    return {
      output: {
        rawResponse: response,
      },
      eventName: 'continue',
    };
  } catch (err) {
    return {
      output: {
        rawResponse: {},
      },
      eventName: 'continue',
    };
  }
};
```
