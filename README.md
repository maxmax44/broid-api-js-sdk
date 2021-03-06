# Broid API Javascript/Typescript SDK

Broid's API SDK javascript module with Typescript support. Check out the [docs](./docs/classes/_index_.broidapisdk.md)
for parameters and usage.

```javascript
const broidSDK = require("@broid/api-sdk");

const botActorID = "";
const auto_reply = true;

const sdk = new broidSDK({
  secretID: '',
  secret: '',
});

const socket = sdk.realtime()
  .then((socket) => socket.subscribe({
		next: (data) => {
      if (!data['@context']) {
        console.log('system: ', data);
        return;
      }

      console.log('user: ', data);
      const actorID = data.actor.id || "";
      if (actorID === botActorID) { return; }

      const dataType = data.type.toLowerCase() || null;
      console.log('sending,,,', dataType);

      if (dataType === 'create' || dataType === 'update' || dataType === 'delete') {
        if (auto_reply) {
          let reply = data;
          reply.to = reply.actor;
          reply.object.content = `ping - ${reply.object.content}`;

          sdk.sendMessage(reply)
            .then(() => console.log('me: ', reply))
            .catch((err) => console.error(err.message));
        }
      }

    },
		error: (err) => console.error(`Something went wrong: ${err.message}`),
	}));

```
