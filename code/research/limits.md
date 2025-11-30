# File Size Limits
# Introduction
To assure file types are **stored in the same addresses** in memory, they are given their own **file size limits**.

If exceeded, the game will **crash due to** (or in an attempt to prevent) **buffer overflow**.

However, some file size limits do not actually match the allocated space in memory, which means **the limits can be increased until it reaches occupied memory**.

For a comparison of the hardcoded file size limits with the actual/maximum limits, click [here](https://github.com/ViveTheModder/dbzbt3-research/blob/main/code/research/csv/file-size-limits.csv). 

If the hardcoded and maximum limits match, then the developers have **specified the correct limit**. Otherwise, the **maximum limit has yet to be found**.