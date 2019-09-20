---
layout: default
title: What is the Value of Quantum Communications?
description: Thinking about where the real values lies in near-term and long-term quantum Communications
---

Outline:
  - Brief description of the current state:
      - Major players (nation-states)
      - Major players (commercial)
      - Current use cases

  - The 'promises' of QComms:
      - 'Unhackable'
      - Can see effect of eavesdroppers

  - The reality of QComms:
      - 'unhackable' within some conditions (these are becoming more stringent)
      - low bitrates
      - QKD is major application in near term, but your info is stored classically!
      - Device Independent implementations are a long way off (Bell states)


  - What do we do with ideal QComms?
      - Your messages are already unhackable, probably (RSA, AES)
      - Why convert classical information to quantum information (serious bottleneck?)
      - Why not use quantum resistant encryptions?
          - Lattice encryption, something else
          - We know that quantum computers can only efficiently solve a subset of classically intractable problems

      - What are we left with:
          - Messages that need to be secret RIGHT NOW, but not later
          - Moving *quantum* information around (from sensors, between processors, etc)
          - Applications in which it is important to guarantee either:
            - total secrecy, guaranteed by known physics (requires Quantum memory so you don't store info classically)
                - maybe undercut by provably quantum resistant encryptions?

            - guaranteed knowledge of eavesdropping attempts
                - No- Cloning means you should have statistically evidence of eavesdroppers

  - What to look for in terms of enabling technologies and advances
      - Error corrected logical qubits (duh, also enables general QC)
      - Good quantum memories
      - High brightness sources of entangled photons (enables high bitrates)
      - High efficiency optical -> something else entanglement conversion (to actually utilize quantum memory)
      - Repeaters (enabled by quantum memory)

# What is the Value of Quantum Communications?

  Practicing physicists and interested observers by now have heard the many fantastical promises of the coming '2nd Quantum Revolution'_[link to something here]_. The usual hype is that quantum computers will smash mere classical encryption and usher in an era of unprecedented, colossal computing power. There's a lot to be said on that subject, but we mustn't neglect the closely related quantum sensing and communications technologies that, together with quantum computing, fall under the umbrella of Quantum Information Sciences. Indeed, sensing and communications are interesting precisely because they have already demonstrated improvements on the classical state of the art _[link to stuff here]_ and are likely to reach marketability long before quantum computers _[link to various Quantum Tech Roadmaps]_.

  The value proposition of quantum sensors is easy to understand, and I intend to author a post about quantum sensing sometime in the future.

  At first glance, it seems that the value of quantum communications (unhackable, perfectly secure communication) is also quite clear. I contend that, even ignoring the substantial shortcomings of current quantum comms technology, the use cases for quantum communications networks are still quite limited.

<h2>The Current State of Play</h2>

Despite its relative youth, the current body of quantum comms literature is intimidating in its breadth and depth. The protocols, implementations, security analyses, tweaks, demonstrations, and so on are multitudinous. As far as I know, there is no good quantum communications primer, except for whichever parts of the quantum computing literature are applicable. Maybe I will write one? Good introductions to quantum computing range from _the_ textbook [Quantum Computing and Quantum Information], to this useful article [War on the Rocks link], or a quantum computing text book slowly being built as a spaced-repetition website [Nielsen and Matuschak].

Nonetheless, no specialized knowledge of quantum mechanics is required to see that the Quantum Communications field is full of activity. There are multiple commercial ventures selling quantum key distribution (QKD) equipment. The Chinese 'quantum' satellite, _Micius_, made a big splash several years ago by distributing quantum keys between ground stations 1200 km apart _[citation]_. Additionally, every major player in quantum technologies has some sort of quantum communications network. The Chinese network spans many nodes in several cities _[source]_, Vienna, Tokyo, and Fermilab are also running more limited QKD networks as well _[source]_. _[Check to see what public programs re: QKD are known in the US]_. In general, the United States focuses more on quantum computing, but still has a very active quantum comms academic research community. China seems to be most advanced, as they not only have demonstrated ground-space QKD, but also are actively working on space-subsurface QKD _[link to blue water QKD studies]_.

Despite all of this activity, quantum communications is currently limited pretty much entirely to QKD implementations. Further, what implementations exist are suboptimal in multiple ways: security proofs often rely on strong assumptions[^1] about many parts of the system, key rates are comically low, and many protocols are stymied by lossy transmission channels. There are entire <a href="http://www.vad1.com/publications/">research labs</a> whose activities are entirely dedicated to exposing security flaws in laboratory and commercial QKD implementations. In the near term, there is not much use to be had from QKD, but as researchers in the field continue to iterate on their protocols, this may change.

<h2> The Promise </h2>

Why? Why do all of this? Why launch what amounts to a full quantum optics lab into space? Why set up quantum communications nodes in major cities?

Pretty much everyone will tell you that quantum communications offer unhackable, provably secure communications channels which, as a bonus, can notify you when a third party is attempting to eavesdrop on your conversation.

Let's leave aside for now the gaping holes in the phrase "provably secure" and consider the case of ideal quantum communications. We can assume the laws of physics as we know them are ironclad, and the bitrates of quantum communication protocols approach those of classical communications.

**Unhackable** messages sound great in principle, but do you need a fully quantum communications infrastructure to realize it? I could encrypt my emails and texts such that even the mighty US government would have no hope of cracking the encryption by brute force. The entirety of our society is built on being pretty sure that no-one can efficiently factor prime numbers. There's no mathematical proof that it can't be done, but clearly such proof is not required.

What circumstances would warrant such ironclad security guarantees?
<ol>
  <li><strong>Wide spread use of universal quantum computers</strong>. A quantum computer with millions or billions of logical qubits could likely make short work of contemporary encryption techniques. It wouldn't grant the ability to intercept and copy quantum information in flight (this is forbidden by the No-Cloning Theorem), nor would it be able to copy unknown states sitting in some quantum memory. However, a cheaper (faster?) option already exists: post-quantum cryptography. There are some tasks that neither classical, nor quantum computers can complete efficiently. Lattice based encryptions rely on the fact that the Shortest Vector Problem _[link here]_ is <em>thought</em> to be hard both for classical and quantum computers. You can expect that post-quantum cryptographic techniques will play a role regardless, as we will certainly still be using classical bits for many tasks.

  The time horizon for this scenario is thought to be somewhere between 5-100 years. My personal guess is at least 50 years.</li>

  <li><strong>Transferring quantum information</strong>. Quantum technology use cases will likely include collecting data from large arrays of quantum sensors. It is possible to read these sensors and transfer their quantum states into classical bits, of course. On the other hand, small distributed sensors may not have much space for quantum-to-classical processing hardware, so transferring quantum information (in the form of qubits) to a centralized processing node may be the most efficient architecture. Necessarily, this process would use quantum comms infrastructure.</li>

  <li><strong>Ensuring immediate integrity of important messages</strong>. Messages that are high priority and time sensitive may benefit from a quantum communication channel. The message itself cannot be copied, only jammed, and the sender/recipient will immediately know. Further, if the content of the message is only import for a short time after receipt, it may not matter whether it is ultimately stored as classical bits, rather than qubits. One specific <a href="https://arxiv.org/abs/1305.0305">use case</a> is enabling fast, secure comms between nodes in the national power grid.  </li> </ol>


I expect that classical information encrypted with whichever <a href="https://csrc.nist.gov/Projects/post-quantum-cryptography/Post-Quantum-Cryptography-Standardization">post-quantum standard</a> NIST chooses will be totally sufficient for the every needs of private citizens, businesses, and most local, state, and federal government entities. Predictably, extraordinarily sensitive government and military communications _might_ benefit from what quantum comms have to offer. I could see something like the nuclear command and control system being run preferentially on quantum nodes, with the classical communications infrastructure as a redundant back-up. Perhaps time-sensitive and extremely secret diplomatic cables would also be a plausible use case. Even so, it's worth pointing out that quantum comms only solve the signals intelligence side of security problem. Information will still be read and known by humans, some of whom will always be vulnerable to overtures from adversarial intelligence organizations.


  <h2> Enabling Technologies </h2>

  The state of the art of quantum communications is still far, far from realistically satisfying the use cases above. Bitrates are low (<kbps over relevant distances), the protocols are largely limited to quantum key distribution, and security proofs have only recently begun to grapple with realistic empirical conditions just to name a few. You still have to do a lot of work crawling through the literature to properly compare various protocols and implementations. Still, here's a list of a few important enabling advances:

  <ol>
  <li><strong> Quantum Memory:</strong> We need to be able to store quantum states for effectively infinite time. Or at least infinite time compared to some error correction and information processing algorithm that might be run on quantum comms nodes. Probably this will come in the form of spin defects in diamond or silicon carbide.</li>

  <li><strong> Entanglement Preserving Frequency Conversion: </strong> It's not particularly likely that quantum memories will be addressable with telecom wavelength photons (1550 nm). Converting 1550 nm photons down to ~850 nm or even up to GHz frequencies while preserving entanglement between the converted photon and some other object is a tall order. Hell, simply doing this conversion at the single photon level with any efficiency is a tall order. Current approaches range from opto-mechanical nano-drums to magneto-electro-optical rare-earth doped crystals. Efficiencies are generally in the single percents, with a few demonstrations of 'high' efficiencies near ~40%. </li>

  <li><strong> Bright Entangled Photon Sources: </strong> It always helps to generate more entangled photons per second. Bright entangled photon sources drive up your 'shots on goal', thereby increasing bitrates just from sheer number of attempts. </li>

  <li><strong> Error-Corrected Logical Qubits: </strong>: This is kind of cheating. Such qubits would be a huge deal across the entire span of Quantum Information Sciences. They'd enable quantum memories, complex algorithms, robust quantum sensors, etc. Hopefully they will become more of a focus in the coming months and years. </li></ol>

The quantum business landscape is currently crowded with all kinds of companies, most of which are peddling some sort of software solutions for some hypothetical future quantum computer. None that I know of are offering anything like what I've described in the list above. In the coming weeks and months I will attempt to survey the business landscape to suss out what exactly is going on and determine exactly how high the bullshit has been piled up.

[^1]: Many security proofs assume key lengths in the 'asymptotic limit'. This is jargon for 'infinite key length'. Finite size key effects are important, but often ignored, especially in the early literature.
