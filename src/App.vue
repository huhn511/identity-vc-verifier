<template>
  <div class="container">
    <div class="row">
      <div class="jumbotron">
        <h1 class="display-4">IOTA Credential Verifier</h1>
        <p class="lead">
          This is a demo application to resolve and verify Verifiable
          Credentials by published Identites in the IOTA Tangle Network.
        </p>
        <hr class="my-4" />
        <p>
          To learn more about Verifiable Crententials, Decentral Identifiers and
          IOTA Identity, checkout this page:
        </p>
        <a
          class="btn btn-primary btn-lg"
          href="https://github.com/iotaledger/identity.rs"
          target="_blank"
          role="button"
          >Learn more</a
        >
      </div>
    </div>
    <div class="row">
      <div class="card" style="width: 100%">
        <div class="card-body">
          <h2 class="card-title">Online Verifier</h2>
          <form @submit.prevent="verify_credential">
            <div class="form-group form-check">
              <label class="form-check-label" for="vc_input">
                Insert Verifiable Credential here</label
              >
              <br />
              <input
                id="vc_input"
                class="form-control"
                v-model="vc_input"
                type="text"
              />
            </div>
            <div>
              <button type="submit" class="btn btn-primary">
                Verify Credential
              </button>
            </div>
          </form>

          <hr />

          <div v-if="result" class="card" style="width: 100%">
            <div class="card-body">
              <h5 class="card-title">Credential Result</h5>
              <p class="card-text">issuer: {{ result.credential.issuer }}</p>
              <p class="card-text">
                <small class="text-muted"
                  >issuanceDate: {{ result.credential.issuanceDate }}</small
                >
              </p>
              <div v-if="result" class="card" style="width: 100%">
                <div class="card-body">
                  <h5 class="card-title">credentialSubject</h5>
                  <p class="card-text">
                    Name: {{ result.credential.credentialSubject.name }}
                  </p>
                  <p class="card-text">
                    <small class="text-muted">
                      name:
                      {{ result.credential.credentialSubject.degree.name }}
                      <br />
                      type:
                      {{
                        result.credential.credentialSubject.degree.type
                      }}</small
                    >
                  </p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";
import * as identity from "iota-identity-wasm-test/web/";

export default {
  setup() {
    const result = ref(null);
    const vc = `{"@context":"https://www.w3.org/2018/credentials/v1","id":"http://company.com/credentials/1337","type":["VerifiableCredential","CompanyCredential"],"credentialSubject":{"name":"Alice","degree":{"name":"Credential of a Company","type":"CompanyCredential"}},"issuer":"did:iota:main:CzonQki7xYGNWUnMzEoWnuPVrCX91nNTJ3BtoJ7yKCTa","issuanceDate":"2020-11-22T20:29:44Z","proof":{"type":"JcsEd25519Signature2020","verificationMethod":"did:iota:main:CzonQki7xYGNWUnMzEoWnuPVrCX91nNTJ3BtoJ7yKCTa#authentication","created":"2020-11-22T20:29:44Z","signatureValue":"23b2Ls2gAeRqA8fwWDTwyTgzwwDcz7EyGtHWhZ7Ct9uhPf7mwYqKo5pc4vGNET215mXRtZ6wA4QhJyAvmt8RCBvGJ1PraTa1QvT5U9QMjVK8FY2R9DLTZEKuTH7fDo6daBWu"}}`
    const vc_input = ref(vc);
    const verify_credential = async () => {
      await identity.init();

      let network = {
        node: "https://nodes.thetangle.org:443",
        network: "main",
      };

      console.log("vc_input.value", vc_input.value);

      let res = await identity.checkCredential(vc_input.value, network);
      console.log("result: ", res);
      // add to vue ref
      result.value = res;
    };

    return {
      vc,
      verify_credential,
      result,
      vc_input
    };
  },
};
</script>

<style scoped>
</style>
