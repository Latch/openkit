# Upcoming Features

| Feature | Description |
| ------- | ----------- |
| Confirming User | As part of the v1.0.0 release of the Latch SDK , the user does not need to authenticate with Latch. In order to receive an authentication Token, the Partner App and Partner BE work together to receive an auth token on behalf of a user. In order to ensure user authenticity without requiring a login, the Latch SDK will implement a security mechanism to ensure the auth token requested by the user is the one that the user received. The user will be required to validate with the Latch SDK security protocol on a regular basis.
| Revoking Consent | As part of initialization, the Latch SDK requests consent from the user by presenting the Latch Terms of Service and Privacy Policy. Once the user accepts and has consented, the Latch SDK can perform the necessary actions to download a list of devices assigned to the user and allow the ability to Unlock. The Latch SDK will implement a mechanism that will allow Partners and Users to revoke consent from Latch.





