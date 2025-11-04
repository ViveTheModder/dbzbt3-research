# Blasts (Special Attacks)

## Introduction
Every character in the game has **two Blast 1's**, **two Blast 2's**, and the **Ultimate Blast**.
Their parameters are stored in the ``skill_param.dat``and ``blast_param.dat`` files of each character costume.

They contain a number of attributes, such as:
* Flags (boolean parameters);
* Number of hits;
* Damage;
* Speed;
* Size;
* Ki Cost (or in the case of Blast 1's, Blast Stock Cost);
* Health Cost and/or Gain;
* Stats (only for Blast 1's).

## Blast Identifiers
In addition, **every Blast comes with an identifier**, aka the **Blast ID**: an unsigned integer which is read by the code **to apply even more attributes**.
Examples of this include:
* ``Babidi's Ultimate Power``, **whose damage is multiplied** by the use of his throw (or Spopovich's Ultimate Blast, if him and Babidi are on the same team);
* ``Devilmite Beam``, **whose damage** (27270 without boost, 30000 with) **is multiplied with a coefficient** (from 0 to 1) determined by the opponent's ``common_param.dat`` file;
* Goku (Mid)'s ``Spirit Bomb``, **whose damage is multiplied by** the use of **his** ``Give me energy!`` **Blast 1**;
* Goku (End)'s ``Super Spirit Bomb``, whose Blast ID is responsible for making the Super Saiyan form **temporarily share lip-syncing** with his base form.

Other Blast IDs are referenced in the code, although **not all of them**. The ones that are referenced **do not lead to anything readable** or even remarkable.

Below are links to **CSV files containing the IDs** for all Special Attacks in the game.
Please note that due to variations of the same Blast (e.g. Blast 2 vs. Ultimate Blast), there may be **more than one Blast ID per Blast**.

## Useful Links
* [Blast 1 Identifiers](https://github.com/ViveTheModder/dbzbt3-research/blob/main/file-types/dat/csv/b1-ids.csv)
* [Blast 2 Identifiers](https://github.com/ViveTheModder/dbzbt3-research/blob/main/file-types/dat/csv/b2-ids.csv)