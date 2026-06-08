<!-- Copyright 2014 Signal Messenger, LLC -->
<!-- SPDX-License-Identifier: AGPL-3.0-only -->

# Signal Desktop - README Fork Addendum

## Fork Notice
This is a UCI CS Capstone research fork of [Signal Desktop](https://github.com/signalapp/Signal-Desktop) and [libsignal](https://github.com/signalapp/libsignal) by Team SorryEve. This fork implements a deniable authentication protocol extension on top of Signal's existing cryptographic stack. The original Signal Desktop README follows below.

## Project Overview - Deniable Authentication for Signal without Trust in the Key Distribution Server

Modern secure messaging systems like Signal rely on a trusted key distribution server to publish and manage long-term identity keys. This introduces a central trust assumption that may be unacceptable in adversarial or high-risk environments. Additionally, standard cryptographic authentication mechanisms leave non-repudiable artifacts, a permanent cryptographic trail that undermines deniability.

This project implements a deniable authentication extension for Signal Desktop using **Private Verifiable Random Functions (PVRFs)** combined with a **Short Authentication String (SAS)** verification layer built on top of Signal's existing X3DH + Double Ratchet protocol. The result is a system where:

- Authentication does not rely on trust in the key distribution server
- No publicly verifiable signatures are produced
- Human-centric identity verification

## Repository Structure

We keep the `main` branch clean in anticipation of a potential upstream pull request or design proposal to Signal. Active development is spread across feature branches. This project spans two forked repositories:

### [Signal Desktop](https://github.com/21pchangkachith/Signal-Desktop) (TypeScript & Electron Client Application)
- `ts\components\conversation\ConversationHeader.dom.tsx branch` - SAS modal UI

### [libsignal](https://github.com/21pchangkachith/libsignal) (Rust Cryptographic Library)
- protocol.rs - Includes modifications to initialize_alice_session and initialize_bob_session to compute the extension to the protocol cleanly. Also includes pvrf_verify for the final step to the extension.

## Team Members

| Name | UCI ID | Email | Role |
|---|---|---|---|
| Phoenix Changkachith | 31787561 | pchangka@uci.edu | Team Lead / Backend Engineer |
| Natasha Jha | 47641148 | natashj3@uci.edu | Backend Engineer |
| Mary Hemker | 45939553 | hemkerm@uci.edu | Frontend Engineer |
| Minh Dang | 90987335 | mqdang1@uci.edu | Frontend Engineer |

## Features Implemented

### Signal Desktop 

- SAS UI - An optional pop-up verification display in the conversation header showing user's contact 6-digit SAS code. Users can confirm whether both sides match before chatting further.
- Member Verification Storage - Each contact will be verified uniquely per device and storaged locally, if a same account with a different device will be required to reverified with the same contact again.

### libsignal

- protocol.rs - Includes modifications to initialize_alice_session and initialize_bob_session to compute the extension to the protocol cleanly. Also includes pvrf_verify for the final step to the extension.

## Architecture

The system extends Signal's existing X3DH + PQXDH protocol with a PVRF-based SAS-MA layer. All protocol computation occurs entirely client-side.

## Setup Instructions

Refer to Signal's [CONTRIBUTING.md](https://github.com/signalapp/Signal-Desktop/blob/main/CONTRIBUTING.md) for setup instructions.

## Local Instructions

Copy `config/local-production.json` content to `config/production.json`

## Testing

### Signal Desktop Unit Tests (Mocha)

Unit tests for the SAS verification logic are located in `ts/test-node/conversations/ConversationHeader_SAS_test.node.ts`.
Since the SAS verification functions are implemented as React hooks inside `ConversationHeader`, the core logic is extracted into standalone pure functions to allow testing without rendering the full component.

#### Function Tests

- `isIndividualSasVerified(conversationId, verifiedMap)` - checks if a 1:1 conversation has been SAS verified
- `isGroupSasVerified(members, verifiedMap)` - checks if all members in a group conversation have been SAS verified
- `markMemberVerified(memberId, verifiedMap)` - returns a new verified map with the given member marked as verified

#### Running Tests

Run our tests as well as Signal's existing tests by using:
`pnpm test`

Run just our tests:
`pnpm mocha --require ts-node/register ts/test-node/conversations/ConversationHeader_SAS_test.node.ts`

### libsignal Unit Tests

Our unit tests for the protocol are located in `libsignal/rust/protocol/tests/session.rs`

#### libsignal invidual tests

- `test_pvrf_mitm_sender_receive_step_resealing()` - Check if the MITM attack can be detected via mismatching SAS and if the protocol works correctly by matching SAS with the attacker
- `test_pvrf_mitm_verify_step_forwarding()` - Check if the MITM attack can be detected on the initiator end per the protocol
- `test_pvrf_mitm_eval_step_forwarding()` -  Check if the MITM attack can be detected on the receiver end per the protocol

#### Running Tests

Run our tests as well as Signal's existing tests by using:
`cargo test`

## Deployment

Not applicable to our project since we are already using Signal application that is already hosted by Signal Server.

## Demo Video

?

## Known Issues

- 1 / 2^20 chance for SAS codes to accidentally match
- Bob/Receiver attaches his proof to every message
- Arithmetic computed on an Edwards Curve can require signage changes since Signal expects all points to lie over the positive axis.
- Protocol cannot be built or tested on Windows OS

## Future Work

### Upstream Integration into Signal

The protocol implementation, along with extensive testing and validation code, has been completed for both libsignal and Signal Desktop. Pull requests have been created to contribute these changes to the Signal project. The next step is review by the Signal development team, whose feedback would guide any further refinements required for integration. If accepted, the protocol could be incorporated into Signal's official releases, enabling SAS-based verification for all users without requiring modified clients.

### Mobile Platform Support

The current implementation focuses exclusively on Signal Desktop. Extending the protocol to Signal's Android and iOS applications would be the next step, ensuring that the verification mechanism is available across all major platforms and enabling a consistent user experience for each device type.


# Signal Desktop - Original

Signal Desktop links with Signal on [Android](https://github.com/signalapp/Signal-Android) or [iOS](https://github.com/signalapp/Signal-iOS) and lets you message from your Windows, macOS, and Linux computers.

[Install the production version](https://signal.org/download/) or help us out by [installing the beta version](https://support.signal.org/hc/articles/360007318471-Signal-Beta).

## Got a question?

You can find answers to a number of frequently asked questions on our [support site](https://support.signal.org/).
The [community forum](https://community.signalusers.org/) is another good place for questions.

## Found a Bug?

Please search for any [existing issues](https://github.com/signalapp/Signal-Desktop/issues) that describe your bug in order to avoid duplicate submissions.

## Have a feature request, question, comment?

Please use our community forum: https://community.signalusers.org/

## Contributing Code

Please see [CONTRIBUTING.md](https://github.com/signalapp/Signal-Desktop/blob/main/CONTRIBUTING.md)
for setup instructions and guidelines for new contributors. Don't forget to sign the [CLA](https://signal.org/cla/).

## Contributing Funds

You can donate to Signal development through the [Signal Technology Foundation](https://signal.org/donate), an independent 501c3 nonprofit.

## Cryptography Notice

This distribution includes cryptographic software. The country in which you currently reside may have restrictions on the import, possession, use, and/or re-export to another country, of encryption software.
BEFORE using any encryption software, please check your country's laws, regulations and policies concerning the import, possession, or use, and re-export of encryption software, to see if this is permitted.
See <http://www.wassenaar.org/> for more information.

The U.S. Government Department of Commerce, Bureau of Industry and Security (BIS), has classified this software as Export Commodity Control Number (ECCN) 5D002.C.1, which includes information security software using or performing cryptographic functions with asymmetric algorithms.
The form and manner of this distribution makes it eligible for export under the License Exception ENC Technology Software Unrestricted (TSU) exception (see the BIS Export Administration Regulations, Section 740.13) for both object code and source code.

## License

Copyright 2013-2024 Signal Messenger, LLC

Licensed under the GNU AGPLv3: https://www.gnu.org/licenses/agpl-3.0.html
