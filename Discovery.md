# Discovery

In order for a sender to make a payment to a receiver they must "discover" some things about the receiver such 
as their ILP address and the currency of their account.

Following this, further information may be required by the sending system such as the full name, public key or 
even a photograph of the receiver for validation by the sender.

## Flow

1. The sender (End User) has an identifier for the receiver such as their account number, mobile number, national identity number 
or similar identifier.
2. The sending system (DFSP) uses this identifer to discover the information required to:
  a. Render an appropriate UI to the sender (name of receiver, currency code of receiving account etc)
  b. Initiate the ILP tansfer (ILP address of receiving account, amount, condition etc)
  c. Get a quote for the transfer
3. The sender confirms the transfer and initiates it via the sending DFSP

## Design Considerations

1. There are a wide variety of identifiers that may be used to initiate the discovery process
2. A lot of the data required is gathered during the Simple Payment Setup Protocol (SPSP)
3. The deployment scenarios will differ and as such the registries of identifiers will have different architectures
(Example: distributed vs centralized)

## Service Discovery vs Data Discovery

Rather than discovering data about the receiver a more extensible solution will resolve the receiver identifier 
to a service endpoint. The service at this endpoint can be standardized (this will be the SPSP entry-point) and in 
future this sevice may be extended to provide additional fucntionality and data.

By decoupling the discovery of the service from the service itself we define distinct phases in the preparation of 
an ILP transfer; discovery and setup. These phases can be defined by distinct entry and exit gates and the specific
implementations can be changed as long as the input and output of each phases is consistent.

Using such an architecture it is also possible to host the service at a URL that does not reveal any data about the
receiver allowing public discovery systems to be used without compromising the receiver's privacy. 

It is assumed that access to the SPSP receiver endpoints will be subject to polcieis that are designed to protect receiver 
data privacy.

## Setup Phase

The setup phase is handled by SPSP. The protocol requires that some entity hosts a _receiver endpoint_ where the sender
can query an SPSP Server for the data required to setup a transfer. All that is required to initiate setup via SPSP is 
the URL of the _receiver endpoint_.

## Discovery Phase

Working back from the requirements to start the setup phase it follows that the discovery phase must simply return a URL. 

### Normalization of Identifiers

Since the inputs to the discovery phase are only loosely typed as "an identifier" it may be useful to be specific and call 
this a URI.

Identifiers that do not have a natural URI form can normally be converted to one (or one can be defined for them). Where an 
identifier is provided to a sending system that is not a URI it is the responsibility of the sending system to determine the 
correct form based on the context and, if required, through interaction with the user.

Example 1: The sender provides the indentifier `+26 78 097 8763` and the sending system recognises this as an E164 format number
and converts it to the URI `tel:+26780978763`.
Example 2: The sender provides the indentifier `bob@dfsp1.com` and the sending system prompts the user to specify if this is 
an emailaddress or an account identifier and then converts the identifier to the form `mailto:bob@dfsp1.com` or `acct:bob@dfsp1.com`.

This normalization allows a more rigid definition of a discovery service such that any service that accepts URIs and returns 
URLs could be used to resolve SPSP receiver endpoint URLs from receiver identifiers.

### Discovery of SPSP Receiver Endpoint URL

Given all that has been defined to this point we can define a discovery service simply as a service that takes a URI representing a 
receiver identifier as input and returns a URL that should be the _receiver endpoint_ for an SPSP server providing services for 
that receiver.

Sending systems (DFSPs) SHOULD determine which discovery service to use based on the URI scheme of the identifier.

The rules for this mapping (URI scheme to discovery service) should be defined as part of each deployment. The Level One project 
should provide implementations of some discovery services to bootstrap ecsosystems where no such thing exists but it should be 
possible for a DFSP to be configured to use other discovery services as long as they meet the minim requirment of resolving a URL 
from a URI.

Therefor the logical steps for a sending DFSP are:
1. Get receiver identifier
2. Normalize identifier to a URI if required
3. Determine which discovery service to use based on URI scheme
4. Resolve URI to URL using discovery service
5. Initiate SPSP at resolved URL

## Discovery Services

The following discovery services MAY be shipped with the Level One OSS solution:

### Central Directory

The central directory may host a simple lookup service that resolves a receiver identifier to an SPSP URL. This type of service 
could be used for any identifier type. This service may also serve as a proxy or aggregator for similar services hosted at the 
receiving DFSPs.

### ENUM

An ENUM service may be hosted using a deployment specific DNS zone. This type of service could be used for any numeric identifier
such as E164 numbers, account numbers or national identity numbers.
