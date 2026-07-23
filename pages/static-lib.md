---
layout: page
title: JUCE Static Library in C++
permalink: /static-lib
---

This is a static library created specifically for developing audio applications in C++ using the JUCE framework. It is designed to speed up development by providing reusable UI components and audio DSP elements, particularly those that are commonly used across applications. This library is currently under development and, at this stage, primarily includes UI components.
<br><br>

## Description

Since this library is specifically designed for development with JUCE, its UI objects inherit from `juce::Component`, the base class for all JUCE UI objects, allowing them to seemlessly integrate into JUCE's graphics pipeline. The UI portion includes common components such as buttons, sliders, and dropdowns, but as well as more complex, audio-focused components such as mixer channels, mixing boards, preset menus, and graphs.

Although JUCE provides standard UI elements such as buttons and sliders, I implement my own versions from scratch as JUCE *Components*. This allows them to be customized and optimized for my workflow, significantly streamlining development. It also makes it easy to integrate them into more complex UI components for this library, such as a mixer channel, which combines sliders, labels, dropdowns, knobs, and buttons.

For the audio DSP portion, the current plan is to develop DSP modules that complement many of the UI elements, such as multi-band equalizers, envelopes, and general effect parameters. This way, whenever I need to implement an EQ, for example, I can assemble the interface using my existing UI components and then integrate a reusable EQ module configured to the required specifications.
<br><br>

## UI Examples

The following are examples of individual UI components created using art assets purchased from UI Mother.


<!-- <video autoplay muted loop playsinline width="100">
    <source src="/assets/vds/fader-test.mov" type="video/mp4">
</video> -->