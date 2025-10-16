# Mimoty relay

Fork of [gzuuus/mimoty](https://github.com/gzuuus/mimoty).

> This relay stills in an experimental/developing phase, by the moment it's just a proof of concept, plase take it a pre-alpha.

## Remote signer relay

This relay enables you to create a **personal** relay with special features. 

The primary goal is to add a remote signing functionality to a personal relay, allowing you to set a root key in the relay that will be used to sign incoming events from subkeys or hosted keys (I'm still deciding on the final terminology, as 'subkeys' is a simpler concept but not entirely accurate, while 'hosted keys' is more accurate but slightly less intuitive).

> @todo  
> This relay could be a Nostr native remote signer, if it implements NIP-46.

These subkeys/hosted keys can be created and managed in the relay dashboard, which uses NIP98 to protect the HTTP endpoints.

When you create a new subkey, the relay will automatically sync the kind 0, 3, and 10002 events from the root key, simplifying the user experience and making the subkeys ready to use immediately. 

The process is straightforward:
1. Create a new subkey
2. Import it into your preferred client or signer, and you're ready to go. 
 
You can then use the created subkey to publish content in the client of your choice, without requiring the client to be compatible with any new features. 

> When you write an event signed with the created subkey, it will be resigned and broadcasted with the root key.
> This means that there are two similar events but with a different signature (one signed by the root key and one signed by the subkey).

One of the key benefits of this relay is that you can create any number of subkeys, enabling use cases such as subkey-per-device. 

Additionally, subkeys have scoped permissions to publish, allowing you to define the specific kinds of events that a subkey can publish. 
>?? are these permissions saved in the database of the relay ??

Another feature of the relay is that it also function as a Web of Trust (WoT) relay, computing your WoT to allow your network to publish in the relay. In this case, events are stored as-is, without being resigned with the root key, whereas events from subkeys are resigned. The relay also actively stores the kinds 0, 3, and 10002 of your WoT, similar to the use case of purplepag.es relay. This enables correct delivery of notes to the inbox of the user you are chatting with.

Note that the implementation is still incomplete, and there is ongoing work to make this a more useful tool.

Highlights:
- Subkeys
- Scoped kind permissions
- WoT relay
- Relay management dashboard

# mimoty.kubo.watch

```bash
# Build binary from main.go
go build
# Run binary
go run mimoty-relay
```

Now the relay is running at port `3666`.


### Development

```bash
go mody tidy
go run main.go
```

## Point to consider

Each parent should be able to create a subkey per child on the relay. This means the relays needs the private key of each parent.
This is a huge security risk (the relay becomes a honey pot with private keys) which gives the relay admin too much power and the ability to abuse this power.
Is this tradeoff worth it? Are there other solutions? Can we replace the private key with a remote signer URL (NIP-46) which can be used to create subkeys?

In the current implementation the permissions are saved locally in a database. Can we nostrify these permissions? See the draft NIP-KK we're working on (Trust Relationships and Delegated Oversight Framework).