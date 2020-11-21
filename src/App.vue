<template>
  <img src="./logo.png" />
  <h1>Hello Vue 3!</h1>
  <button @click="create_did">Create DID.</button>
  <button @click="create_did2">Create DID 2.</button>
  <pre>{{ did }}</pre>
</template>

<script>
import { ref } from "vue";
import identity, * as identity_lib from "iota-identity-wasm-test/web";

export default {
  setup() {
    console.log("identity", identity);

    identity().then((instance) => {
      console.log("instance", instance);
      const { key, doc } = instance.Doc.generateCom();

      console.log("key (generated)", key);
      console.log("doc (generated)", doc);

      console.log("doc (unsigned)", doc.document);

      doc.sign(key);

      console.log("doc (signed)", doc.document);

      console.log("doc valid?", doc.verify());

      const json = JSON.stringify(doc.document);

      console.log("From JSON >", instance.Doc.fromJSON(json));

      // instance
      //   .publish(doc.document, { node: "https://nodes.thetangle.org:443" })
      //   .then((_res) => {
      //     console.log("published", _res);
      //   });

      // console.log("resolved", await res.resolve(alice_document.did, { node: "https://nodes.comnet.thetangle.org:443", network: "com" }))
    });

    const keyPair = ref();
    const did = ref();
    console.log("identity_lib", identity_lib);

    const create_did = () => {
      identity_lib.newKey().then((_keyPair) => {
        keyPair.value = JSON.stringify(_keyPair);
        console.log("keyPair", keyPair);
        identity_lib.newDID(_keyPair.public).then((r) => {
          did.value = JSON.stringify(r);
          console.log("did.value", did.value);
        });
      });
    };

    const create_did2 = () => {
      identity().then((lib) => {
        let keyPair = new lib.Key();
        console.log("keyPair", keyPair);
        did.value = JSON.stringify(new lib.DID(keyPair.public));
        console.log("did", did.value);
      });
    };

    // let key = new identity.Key();
    // console.log("key", key);

    return {
      did,
      create_did,
      create_did2,
    };
  },
};
</script>

<style scoped>
body {
  max-width: 800px;
}
img {
  width: 200px;
}
h1 {
  font-family: Arial, Helvetica, sans-serif;
}

pre {
  height: 50px;
  width: 100%;
  overflow: auto;
}
</style>
