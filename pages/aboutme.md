---
layout: page
title: About Me
permalink: /aboutme
---

I earned my B.S. in Computer Science in May 2026, but my experience in software development dates back to my self-taught years. I began learning C++ in 2016, and before long I developed an almost insatiable hunger to learn more. As my skills grew, I found myself creating simple programs and taking on increasingly challenging projects. Through that process, I discovered what it truly means to be passionate about something. It's the willingness to face any challenge, no matter how big or complex, and the determination to push through it. It's also the curiosity that continuously drives me to learn more and grow as a software engineer. Finally, it's what makes this journey both fulfilling and rewarding.

## What I've done
- The majority of my self-taught years have been spent developing audio plug-ins for DAWs in C++ using the JUCE framework. This stems from my background in music and my growing curiosity about audio technology.

    - I've developed both GUI and audio DSP components.

    - I've developed fully functional audio plug-ins, including a drum sample library, a graphic EQ, audio effects, and custom user interfaces.

        - Since many of these projects were created early in my learning journey, they aren't currently posted in my Projects section. As explained [below](#what-im-doing-now), I'm redeveloping many of them and will publish them once they're complete.

        - The drum sample library plug-in was my most ambitious project and remains the one I'm most proud of. It's a virtual drummer that reads MIDI notes and plays the corresponding audio files. In 2017, I wanted to create something that could compete in the drum sample library market, which meant it had to sound as human as possible. At the time, cymbals were especially notorious for sounding robotic, so I developed a concept that solved that problem. I describe this project in more detail [below](#what-im-most-proud-of).

    - I began doing freelance work on audio software in 2020, though it remained fairly limited until I joined Spectre Digital as a freelance developer in 2023. More details available in the [Skills & Experience](/skills-experience.md) section.

- I began focusing more heavily on Java and Python in 2023 when I started my undergraduate studies in Computer Science at Colorado State University. During my studies, I also gained experience with RESTful APIs, integration and unit testing, distributed systems, operating systems, and computer architecture.

    - My previous software development experience enriched my undergraduate experience far more than I anticipated. Because I was already proficient in object-oriented programming and familiar with several related concepts, I was able to strengthen my understanding where needed while also spending more time exploring new concepts, technologies, and frameworks.
    
    - For many course projects, I went beyond the course requirements by adding features, optimizing code, refactoring, and expanding functionality. These projects include TCP/IP networking, data analysis with Apache Hadoop MapReduce and Apache Spark, multithreaded programming, and more, all of which are showcased in the [Projects](/projects.md) section.

## What I'm doing now
- Currently seeking a full-time software engineering position.

    - Through my degree, personal projects, and freelance work, I've gained valuable experience that has prepared me well for a professional software engineering role. I'm excited to take on new challenges and continue growing as an engineer.

- Building a personal C++ static library to streamline audio plug-in development with the JUCE framework. At the moment, it consists primarily of custom GUI components such as buttons, sliders, and audio mixer components (fader, pan, channel), but the plan is to also include DSP components as well as more complex GUI components such as equalizers.

    - The main purpose of this library is to speed up the development process for my personal audio projects. It significantly reduces repetitive development tasks, allowing me to focus more on the core functionality of each project.

    - I plan to use this library to redevelop many of my early projects as previously explained under [What I've done](#what-ive-done), including the drum sample library plug-in.

- Reviewing calculus and linear algebra to fill in gaps and further solidify my understanding.

- Expanding my experience developing in a Linux environment.

## What I'm most proud of
- I independently developed a fully functional drum sample library plug-in for Digital Audio Workstations (DAWs) designed to compete in the professional audio production market. It's a virtual drummer that reads MIDI notes and plays the corresponding audio files.

    - In 2017, I developed an approach for combining the drum sample recording process with the plug-in's playback engine to produce the most human-sounding performance possible. Since cymbals were often the least convincing part of drum sample libraries, I also designed a solution to address that challenge.

    - I directed the drum sample recording sessions in professional studios and completed all sample editing, ensuring the final samples met my design specifications.

    - During testing, many audio producers and professional drummers described the performances as sounding virtually indistinguishable from those of a human drummer.

    - This project advanced to the point where I was finalizing a distribution agreement, but it ultimately didn't move forward. As a result, I'm unable to showcase the plug-in because the drum samples and art assets were exclusive to that agreement. However, I plan to fund new drum recording sessions so I can demonstrate the plug-in without any licensing restrictions.