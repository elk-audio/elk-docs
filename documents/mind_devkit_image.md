## MIND Music Labs Development Kit Image

This is a VirtualBox LUbuntu 18.04 image with basic developer tools installed
and guest extensions for VirtualBox version >= 5.2.12.

THE SOFTWARE IS PROVIDED "AS IS" AND MODERN ANCIENT INSTRUMENTS NETWORKED AB (MIND) DISCLAIMS ALL WARRANTIES WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR ANY SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.
Sushi is covered by the license included in `/home/minddev/work/sushi/LICENSE`.
By using Sushi, you agree to its license terms.

The other software components provided in the Virtual Machine are covered by their license (GPL and others).

# Running the Virtual Machine

We recommend using [VirtualBox](https://www.virtualbox.org/wiki/Downloads), and have not tested using the provided disk image on other Virtual Machine software hosts.

Start VirtualBox, and there:

1. Create a new image with type=“Linux” and version=”Ubuntu 64bit”

2. Select “Use an existing virtual hard disk file” and point to the downloaded .vmdk file
3. Select the number of CPUs in Settings/System and eventually video memory under Settings/Display, suitable for the performance you want versus what your computer is capable of
4. (optional) Setup a shared folder on your local hard drive
5. Launch the VM image, password for user minddev is: xcompile
6. Open a terminal, by clicking on the quick-launch icon on the bottom-left corner or by typing Alt+F2
followed by “lxterminal”

# Login details

*user* : minddev
*password* : xcompile

# Steinberg SDKs

Steinberg VsT SDKs, which are needed for build of VsT plugins without JUCE, are not included.
Get them from Steinberg and place them inside `/home/minddev/SDKs/`

# Directory structure

Most of the relevant ELK-related components are placed in the home directory of `minddev` user (`~` in the following):

  * `~/audio`   : useful audio test tones to test FX plugins, 48KHz stereo 24bit .wav
  * `~/bin`     : ELK-related binaries and helper scripts
  * `~/doc`     : ELK documentation files, in Markdown and .pdf format
  * `~/plugins` : prebuilt binaries for MDA example plugins
  * `~/work`    : developer tools and libraries

# Included tools

These are all installed in `/home/minddev/work` :

  * `vst2-cmake` : CMake project for (cross) compiling VsT2 plugins. See README.md in that directory
  * `vst3-template` : CMake project for VST3 plugins, with examples for RT/non-RT communication.
  * `JUCE` : JUCE framework (redistributed in GPL form), with Projucer built for this machine (installed in ~/bin), patches to allow the creation of headless Linux VST plugins that can be hosted by Sushi running on ELK dev boards.
  * `juce-examples` : custom examples of Linux headless plugins to be tested in Sushi (including a 4 in / 6 outs plugin)
  * `MrsWatson` : offline VsT2 host for testing purposes, redistributed under BSD license
  * `sushi` : MIND's audio host compiled for this virtual machine, can be used to test plugins before running them  on the board, either offline or in real-time with JACK/ALSA. See README.md in the directory
  * `twine` : library for easily accessing Xenomai subsystems from audio plugins. Full source code included.

Other significant tools installed system-wide:

  * JACK audio toolkit : start the JACK server using Cadence (already configured), or the `start_jack` script.
    Only audio output is supported by VirtualBox, and latency is very high
  * Catia : JACK graphical patchbay to connect JACK applications (including sushi)
  * Carla : JACK VST host that can be used to test plugins (including GUI)
  * Audacity : load .wav files here and connect it to Sushi to simulate physical input signals

