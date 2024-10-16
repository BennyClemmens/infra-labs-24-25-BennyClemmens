# Infrastructure Automation - Lab assignments

- Student name: Benny Clemmens
- Repository URL: <https://github.com/BennyClemmens/infra-labs-24-25-BennyClemmens>

This repository contains the lab assignments for the course Infrastructure Automation, which is part of the [Bachelor Applied Computer Science programme](https://www.hogent.be/opleidingen/bachelors/toegepaste-informatica/) at [HOGENT](https://www.hogent.be/).

The goal of these assignments is to get familiar with concepts like Infrastructure as Code, container virtualization, CI/CD, monitoring, etc.

The assignments can be found in the [assignment](assignment/) directory.

This repository also contains some scaffolding code for setting up virtual environments so students can get started quickly. They are based on [ansible-skeleton](https://github.com/bertvv/ansible-skeleton), a framework for quickly setting up an Ansible development and testing environment powered by [Vagrant](https://vagrantup.com).

- [dockerlab](dockerlab/): sets up a VM to experiment with container virtualization.
- [vmlab](vmlab/): a lab environment that, when you're finished, will consist of several VMs set up using [Ansible](https://www.ansible.com/).

The code works on Windows, macOS and Linux. The setup is meant to be run against VirtualBox as virtualisation platform. VMWare workstation should also work (thanks to the contribution of a student), but this is not tested by the course lectureres.

## Author/License information

This assignment and the scaffolding code was written by [Bert Van Vreckem](https://github.com/bertvv/).

The assignment and all documentation are shared under the [Creative Commons Attribution 4.0 International](http://creativecommons.org/licenses/by/4.0/) license. All code (both scaffolding and testing code) is subject to the MIT license. See [LICENSE.md](LICENSE.md) for details.

Questions and remarks about this assignment are welcome (use the Issues), as well as improvements, fixes, etc. (you can submit a Pull Request). However, technical support on getting the setup working, or on solving the assignment is reserved to students take the courses for which it was developed.
