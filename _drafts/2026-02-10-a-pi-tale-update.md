---
category: article
---
# a Pi tale update

Switching from Arch to Alpine, migrating from Docker to native, and a redesign to my website.

I was planning to write the third part and possibly the ending to my self-hosting on Pi tale, and I still am. But since the previous post, there have been a few issues with my Arch Linux ARM setup. It was bleeding edge, sure, but it was also breaking package dependencies every now and then. That interfered with the Docker setup where I host this website and pretty much everything else.

Package support on Arch Linux ARM is also quite limited compared to the main Arch Linux branch, given the two projects are not directly related and the ARM branch has to build the ARM native packages themselves.

As such, I made the decision to switch the operating system from Arch Linux to Alpine Linux and dialed down the CPU clock to 1.8GHz, which should be much easier on the Pi (not that I just learned boosting to 2.1GHz can void your warranty, and I don't find actual use from boosting that much anyway.)
