Email from a TUF project core member to prepare for an introductory meeting
between TUF and CPAN people:

Below is a summary of what I think TUF is good for and what it does not
handle, and another section with my thoughts on artifact repository signing in
general: I'm not sure how far you are with your ideas/designs so feel free to
disregard if that does not sound useful to you.


## What is TUF good for?

TUF is a great delivery method for 3 things:
identities (typically public keys)
delegations of trust (decisions on which identities should be trusted)
signatures over artifacts (artifact hashes to be specific)
Whenever you expect a number of client application instances to make trust
decisions based on the above things, TUF may be a good choice. The critical
consideration is this: _TUF only provides the delivery method for those
things_. Management of identities and trust delegation as well as the actual
signing process will be more or less system specific.

There are TUF software projects under way to make this a more out-of-the-box
experience but currently implementing TUF _especially for author signing_
requires significant amounts of system design and software development.


### Potential approaches to improving artifact repository security

This section may turn out to be useless to you -- I don't know how far you
have thought this through already -- but I'll include this just in case as I
had a text ready that only required small modifications.

I believe these are the main ways an artifact repository can improve their
security with signatures and/or attestations. I'm putting these in imagined
and rough order of increasing difficulty:

1. Repository to end-user

   The repository can start signing repository states and individual
   artifacts, and clients can start verifying those. The potential security
   impact is fairly low (as this does not include author signing) but also the
   impact on any workflows would be fairly low.

   This only requires changes in the repository software itself initially but
   when the clients want to start using the signed data, they will need a good
   delivery method for identities: TUF is a decent choice here, there may even
   be software available that fits your requirements.

2. Author to repository

   Verifying the author's identity in some way before it is published to users
   is clearly a good idea.  There are several approaches here, some of them
   easier than others -- as Z##### mentioned some of them are likely to not be
   acceptable to some communities.  Some examples with wildly varying
   complexity:

   * PyPI just started with a minimal approach: not even signing but verifying
     the build system identity
     https://blog.pypi.org/posts/2023-04-20-introducing-trusted-publishers/
   * Verifying actual build provenance from trusted builders: SLSA (sigstore)
     / intoto
   * Verifying that the uploaded artifact is signed by an author trusted by
     the repository

   In addition to build tool integration, the repository needs a trust
   delegation design:

   * how do you make decisions to delegate trust to specific identities?
   * How do you change those decisions later on? -- think accidents with
     private keys etc
   * How do you handle different levels of trust? -- are the new mechanisms
     opt-in?

   These decisions affect how useful the security improvements will be: if the
   trusted signing identity can just be changed on a whim by logging in to a
   web UI, then impact will be limited.


3. Author to repository to end-user

   End users verifying the artifact author signatures and/or build provenance
   is the holy grail here, we'd all like to see that. This requires quite a
   few pieces to be in place though:

   * A practical and complete trust delegation _decision making_ mechanism.
   * Support in the repository software for that mechanism (how do you change
     authors, etc)
   * Signing/provenance tools integrated into build tools
   * Identity & delegation delivery mechanism (like TUF) integrated into
     client tools and the repository software

   The issues here are mainly that any design decisions are likely to have
   major effects on actual workflows (both artifact author and repository
   maintainer). I'm not aware of any implementations yet in the open source
   artifact repo space, and significant design and implementation work is
   likely still required.
