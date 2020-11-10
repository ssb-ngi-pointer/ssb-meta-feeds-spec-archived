# SSB meta feed

Status: Design phase

Feeds in SSB are the main abstraction. It is possible to entangle
multiple feeds by referencing messages in other feeds, but otherwise
feeds are independent. Furthermore there is no concept of any kind of
metadata about a feed. This could be the lifetime of a feed, what the
feed is about, the format of the messages or other things people might
come up with.

Over time a number of different use cases for reasoning about how
multiple feeds relate to each other has come up. This includes same-as
where multiple devices has independent feeds, but can be seen as
belonging to the same physical person. Feed rotation for switching to
a newer feed format. Or it might be being able to say something about
a subset of messages from a feed (a claim), that will enable partial
replication of those messages.

Another aspect of existing feeds in SSB is that they tie the identity
of the feed together with the contents of the feed.

While adding a new core abstraction to SSB can be seen as a big
change, we believe the abstraction adds enough expressive power that
it makes up for this.

A meta feed consists of a master key and a state. The master key is
the only key able to update the state. State is defined as a number of
feeds with a identifier and a set of metadata fields. The metadata
fields are left open to the implementation.

FIXME: define what the state is exactly and how it is updated

When migrating from an existing SSB feed to a meta feed, for
simplicity the master key would be the same as the original feed. For
new feeds the master key should be different an used to derive keys
for the sub feeds. FIXME: keks?

Once you start talking about multiple feeds that might relate to the
same thing (say contact messages of a feed) is becomes very important
what the purpose of the feeds are and how they are stored so you don't
end up with duplicate messages.

Let us see how we can use the above abstraction to solve several
common examples:

## Same-as

Same-as (defined as the case where it would be ok that multiple feeds
would be under the control of one master key) could be implemented
using this by adding the different feeds to the meta feed and
assigning them metadata that would define them all as active. If a
device is lost it is simply removed from the state. This of course
assumes that the master key is stored in a safe place and is never
lost.

## New feed format

Changing to a new feed format could be implemented by adding a new
feed to the state and assigning that as active. 

In case of backwards compability with clients that does not support a
newer feed format or in the case of only wanting to support newer feed
formats, maintaining muliple feeds with the same content would be an
interesting avenue to explore. As the hash of the messages in the two
feeds would be different, there could be a way to include the hash of
the corresponding message in old feed in the newer feed.

Lower end clients could offload this extra storage requirement to
larger peers in the network.

## Claims

If one would like to replicate a specific part of a feed, such as the
contact messages, one could request another peer to generate a feed
that only contains these messages. This would only act as a claim,
never a proof that some messages were not left out. Naturally this
come down to trust then. Using the friend graph would be natural, as
would having multiple author staking claims and entangling these.

## Subfeed

Similar to claims it would be possible to create subfeeds that would
only contain certain messages. This might be useful for specific apps
or other use cases. Another use case for this would be curated
content, where specific messages are picked out that might be of
particular interesting to a certain application or specific people.

## Ephemeral feeds

Using the metadata it would be possible to attach a lifetime to feeds,
meaning honest peers would delete the feeds after a specific time.

FIXME: consider the broader consequences of ephemeral feeds. Maybe
they can only be used in limited circumstances.

## Allow list

Similar to ephemeral feeds it would be possible to attach an allow
list to a feed and only distribute this feed to people on the allow
list. As with ephemeral feeds, this cannot be enforced, but assuming
honest peers would give piece of mind that the data is only stored on
a certain subset of the whole network. This come naturally be combined
with private groups to better ensure safety.

# Acknowledgments and prior work

CFT suggested the use of meta feeds
[in](https://github.com/arj03/ssb-observables/issues/1)
