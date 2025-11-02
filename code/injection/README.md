# How to do Simple Code Injection for DBZ Budokai Tenkaichi 3 (PS2)

## Prerequisites
* Basic knowledge of Assembly
* Basic knowledge of C

## Requirements
* Any hex editor out there (in this case, [010 Editor](https://www.sweetscape.com/download/010editor/))
* [Cheat Engine](https://github.com/cheat-engine/cheat-engine)
* [DBZBT3 Unused Functions CSV](https://github.com/ViveTheModder/dbzbt3-research/blob/main/code/injection/csv/dbzbt3-unused-functions.csv)
* [DBZBT3 Unused Methods CSV](https://github.com/ViveTheModder/dbzbt3-research/blob/main/code/injection/csv/dbzbt3-unused-methods.csv)
* [Ghidra](https://github.com/NationalSecurityAgency/ghidra/releases)
* [PCSX2](https://github.com/PCSX2/pcsx2/releases)

# Setup Guide
1. Download the [Ghidra Emotion Engine: Reloaded](https://github.com/chaoticgd/ghidra-emotionengine-reloaded/releases/tag/v2.1.12) extension 
and put its ZIP file in ``{Ghidra Path}/Extensions/Ghidra``.
2. Open Ghidra, go to File -> Install Extensions and check the checkbox next to ``ghidra-emotionengine-reloaded`` to install the extension.
3. From there, go to File -> New Project (CTRL+N) and specify the directory and name of the project.
4. Then, go to File -> Import File (I) and import [this ELF](https://github.com/HiroTex/dbz3_sc/blob/main/ghidra/slps_258.15.gzf).
5. After that, double-click ``slps_258.15`` to get to the decompiled source code.

# Brief Explanation
This means of code injection does NOT add additional binaries (more ELFs) or code to the main ELF (Executable and Linkable Format, in this case ``slps_258.15``). 
Instead, it utilizes several functions and methods that are unused, as free space for the injected code. 

That is why I have provided the prototypes/signatures, addresses, and sizes (in bytes) of each unused function/method.
To clarify, the addresses belong to the NTSC-J (Japanese) version of the game, since only that version has been decompiled.

Different versions of the game may contain different functions/methods, all of which may be placed differently throughout the code. 
As a result, jump instructions (``j``, ``jal``) are different from one version to another, because they take an ABSOLUTE location as a parameter.

Branch instructions (``b``, ``beq``, ``bne`` etc.) could also be affected, but ONLY when more code is added. 
That is because branches take a RELATIVE location as a parameter, expressed by the number of instructions below that the branch will skip.

If the number of instructions remains unchanged for that function/method, then no matter the game version, the branches are also unchanged.

# How to find Equivalent Functions/Methods in Other DBZBT3 Versions
The rest of this tutorial will use the ``BattleActor::CurrentStageIsTournament(void)`` method as an example. 
That is because I wanted to go for something simple, especially for my first time trying out code injection.

![ghidra-1](https://github.com/ViveTheModder/dbzbt3-research/blob/main/code/injection/img/ghidra-1.png)

Said method is located at address 0x001DC3E8, and its instructions can be viewed straight from the game memory. 
To do this, either Cheat Engine or PCSX2's in-built debugger can be used.

Now, to find this method in the NTSC-U (USA) version of the game, the PCSX2 Debugger will NOT help, hence why Cheat Engine is a requirement.

Here are the steps that must be followed AFTER finding the method in memory (or viewing it in Ghidra):
1. Write down the initial bytes of this method, aka the contents of the very first instructions, 
UNTIL a ``j`` (jump) or ``jal`` (jump and link) is encountered.

In this case, the initial bytes are ``F0 FF BD 27 00 00 BF FF``. That is clearly not enough, because 1062 other functions/methods start with that exact sequence.

2. If the byte search leads to more than 1 result, then include the last instructions from an adjacent function/method.

Here, I took the last 3 instructions from ``BattleActor::FUN_001DC390`` and added them to the search:

``03 08 00 46 08 00 E0 03 10 00 BD 27 F0 FF BD 27 00 00 BF FF``

3. While in Cheat Engine's Memory Viewer, use the CTRL+F shortcut, select the ``(Array of) byte`` option and paste in the byte search.

![ce-5](https://github.com/ViveTheModder/dbzbt3-research/blob/main/code/injection/img/ce-5.png)

4. Look at the bottom-left corner of the Memory Viewer and copy the address.

![ce-6](https://github.com/ViveTheModder/dbzbt3-research/blob/main/code/injection/img/ce-6.png)

As a result, in the NTSC-U version, the ``BattleActor::CurrentStageIsTournament(void)`` method is located at 0x201DC480.

Cheat Engine originally took me to 0x201DC474, but because I included 3 unrelated instructions, I moved the address by 12 bytes, making it 0x201DC480. 

The same logic can be applied to finding the function/method directly in the ELF file. In the ELF of the NTSC-U version, slus_216.78, the method is located at 0x000DD480.

## How to view Functions/Methods in Memory - The Cheat Engine Way
1. Go to File -> Open Process and select PCSX2 from the Process List.
2. Click the ``Memory View`` button.
3. While in the Memory Viewer, use the CTRL+G keyboard shortcut and simply paste in the address, but replace its leftmost digit with a 2.

![ce-1](https://github.com/ViveTheModder/dbzbt3-research/blob/main/code/injection/img/ce-1.png)

![ce-2](https://github.com/ViveTheModder/dbzbt3-research/blob/main/code/injection/img/ce-2.png)

![ce-3](https://github.com/ViveTheModder/dbzbt3-research/blob/main/code/injection/img/ce-3.png)

![ce-4](https://github.com/ViveTheModder/dbzbt3-research/blob/main/code/injection/img/ce-4.png)

## How to view Functions/Methods in Memory - The PCSX2 Debugger Way
1. This may differ for later versions of PCSX2 (after 1.6), but go to Debug -> Open Debug Window...
2. Make sure the game is running. Otherwise, the debugger's memory viewer will show question marks.
3. Use the CTRL+G keyboard shortcut and simply paste in the address, but replace its leftmost digit with a 2.

![pcsx2](https://github.com/ViveTheModder/dbzbt3-research/blob/main/code/injection/img/pcsx2.png)

# Code Injection Examples
## Try It Out!
1. [Added Ring-Out to 1 map (Mountain Road - Evening)](https://github.com/ViveTheModder/dbzbt3-research/blob/main/code/injection/mods/add-ringout-to-maps/SLUS_216%20(v1).78)
2. [Added Ring-Out to 4 maps (all Desert maps, Planet - Night, and Mountain Road - Evening)](https://github.com/ViveTheModder/dbzbt3-research/blob/main/code/injection/mods/add-ringout-to-maps/SLUS_216%20(v2).78)
3. [Added ability to view damaged costumes in Character Reference; imported from DBZBT4](https://github.com/ViveTheModder/dbzbt3-research/blob/main/code/injection/mods/view-damaged-costumes)

## Demonstration
![example-1](https://github.com/ViveTheModder/dbzbt3-research/blob/main/code/injection/img/example-1.png)

![example-2](https://github.com/ViveTheModder/dbzbt3-research/blob/main/code/injection/img/example-2.png)

![example-3](https://github.com/ViveTheModder/dbzbt3-research/blob/main/code/injection/img/example-3.png)

## Breakdown
There's more than one way to skin a cat, as they say. My approach may not be the best, but it sure as hell works.

So, to get a general idea of the ``BattleActor::CurrentStageIsTournament(void)`` method, I divided it into sections based on each instruction:
1. Initialization (first 6 instructions)
2. Map Check 1 (next 2 instructions)
3. Map Check 2 (next 2 instructions)
4. Setting the result variable to true (next 3 instructions)
5. Returning the result variable, which is false by default (last 2 instructions)

The actual code itself is pretty simple: get current map ID, compare it to two other map IDs, then return true if the IDs match.

![ghidra-2](https://github.com/ViveTheModder/dbzbt3-research/blob/main/code/injection/img/ghidra-2.png)

In order to add more map checks to the code, I found three unused methods next to each other and zeroed out their instructions to make some space.
I also copied the remaining 12 instructions of the method above, zeroed them out and moved them to that empty space.

One question remains: how does one point one instruction set to another? The answer: writing a new jump instruction, by debugging... or memorizing the exact byte sequence.

Better yet, why not "assemble the instruction yourself" by learning more about [MIPS Assembly Instruction Formats](https://en.wikibooks.org/wiki/MIPS_Assembly/Instruction_Formats)?

Splitting up the code earlier assured that only one jump instruction is used, rather than several, but multiple jumps can still be a valid approach.

Back to the map checks for a bit, each one consists of loading a constant to a register and then comparing it to the register with the current map ID. 
The checks are placed in such a way that if one check fails, the next instruction is executed, aka another map check. 
If a map check is successful, its corresponding branch simply redirects to the ``jr`` (Jump Register) instruction, terminating the method.

When adding more map checks, it is important to assure every branch after the check points to the correct instruction.

## Sample Byte Sequences of Instructions & Lines of Code
* ``jr ra`` ----------------> ``08 00 E0 03`` (Jump to Return Address Register)
* ``return 0`` -------------> ``2D 10 00 00`` (0 = false; move contents of ``zero`` register to ``v0``)
* ``return 1`` -------------> ``01 00 02 24`` (1 = true; load immediate to ``v0``)
* Move X instructions down -> ``XX 00 00 10`` (unconditional branch; if conditional for ``zero``, then it's closer to ``40 10`` for ``beq`` & ``40 14`` for ``bne``)

## How to write New Instructions
1. This may differ for later versions of PCSX2 (after 1.6), but go to Debug -> Open Debug Window...
2. Boot up the game, but also assure that the game has been paused WITHOUT going to System -> Pause. 
3. To do this, either press F12 or go to Capture -> Video -> Start recording. It should bring up a Capture System window that should be closed later.
4. Back to the debugger, it comes with a disassembler. Right-click any instruction there, and go to Assemble Opcode.
5. Then, enter the instruction you want to compile.
6. EXTREMELY IMPORTANT: make sure to use registers that are NOT already occupied by the same function/method or another concurrent one.
7. After that, write down the bytes obtained from that new instruction.
8. Changes made to the ELF will also change the CRC, which is a checksum PCSX2 uses for savestates and proper game recognition.
   Therefore, it is recommended to [use this tool](https://en.wikibooks.org/wiki/MIPS_Assembly/Instruction_Formats) to fix the CRC.
