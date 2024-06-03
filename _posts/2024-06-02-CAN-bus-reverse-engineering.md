---
layout: post
title: "CAN bus reverse engineering of an electronic turbocharger"
date: 2024-06-02
category:
  - "Mortumm"
---
## The idea

A race car driver friend of mine, had an idea to make use of a electronic turbo that landed on his lap by chance.

The point there is to have an immediate and finely tuned response based on our algorithm that is a product of the information given by the ECU (Engine Control Unit) such as engine RPM and the amount of boost based on it with timings. This is rather different approach than the typical turbochargers that benefit the power producing process with a distinct delay, through compression of air intake for combustion.

## What we're working with

CAN as in Controlled Area Network, is a serial communication platform with widespread adoption in automotive and vehicle industry. Usually depicted with words such as efficient and reliable, albeit the practical and / or the end user experiences vary.

Arduino with a CAN bus shield is a straightforward and cheap option to start with. The serial sniffing / decoding setup is a work in progress, will have to do some digging first to determine how to access the required information, or better yet, if a already familiar tool like Wireshark is suitable for extraction, and then on to decoding it.

A completely different microcontroller for real-time control system could be a requirement in the real world use case, but Arduino platform gets us started.

## What is known and not known

eTurbo is used in certain VAG produced higher end vehicle models. It was quickly removed and replaced with a similar option, this explains the ready availability of such part. Replacement reasons are unknown.

The eTurbo requires ~~38-42 Volts~~ 48V for operation, colleague already has a solution for this. The information passed by the ECU to the CAN bus is still unknown. Hence the sniffing.

Safeguards for situations such as overboosting or voltage overload / draw. Proceeding with checkpoints to make sure it is safe and working as expected.
Our safety is priority one, but also for the parts used and when ready for actual testing the vehicle itself. Necessary precautions are made to ensure these.

The connector type required is a off the shelf type, although the need for modification and customization can be circumvented with 3D printing the connector for our specific needs and testing.

There are similar projects already, but the information gathered from these is not publicly available.

## Whats next

The work ahead of us progresses in bursts, since we happen to live more than 300 km apart from each other, and the time we have available to further this is in a pretty tight spot. Going to be slow but steady progress.

Nonetheless, this is a interesting project to work on, but it is also a great continuation to my past project experience in the automotive industry.
