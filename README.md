# SocialDancePlaylistGenerator
An automated playlist generator for social dancing, designed to create well-structured dance mixes for genres like folk, ballroom, Latin, and swing. This program uses TIDAL API to fetch songs from predefined playlists, ensuring smooth transitions between tempos and dance styles while balancing fast and slow dances. The general rule for picking is: 2 tracks of the same subgenre followed by a pause track containing 4 seconds of silence.

The following rules define how subgenres are selected when generating the playlist. The system ensures variety, prevents consecutive repetitions of similar styles, and balances pacing.

---

### **1. Genre Selection**

1. **Weighted Random Selection**
   - Genres are picked based on the `GENRE_WEIGHTS` dictionary.
   - Example: If "Lattarit" has a weight of 4 and "Swing" has a weight of 2, "Lattarit" is twice as likely to be picked.

2. **Avoid Consecutive Repeats**
   - The script avoids picking the same genre consecutively unless no other options remain. 

---

### **2. Subgenre Selection**

1. **Filter Available Subgenres**
   - Only subgenres with tracks **not yet picked** are considered.
   - A dictionary of `valid_subgenres` is created from `available_playlists`, ensuring:
     - The subgenre exists in the available playlists.
     - It contains tracks that haven’t been picked yet.

2. **Group-Based Blocking to Prevent Consecutive Similar Picks**
   - The first word of the subgenre name defines its group.
   - Example:
     - "Bachata" and "Bachata hidas" are treated as the same group.
   - **Exceptions**:
     - Some subgenres are grouped together even if they don’t share the same first word.
     - Example: *"Sottiisi"* and *"Jenkka"* are treated as the same group due to their similar musical style and dance characteristics.
   - The script **avoids picking a subgenre from the same group twice in a row**, unless no alternatives exist.

3. **Fast-Paced Dance Restrictions**
   - If the last pick was a **fast-paced dance**, the next pick **must not** be from `FAST_PACED_DANCES`, unless no other options remain.
   - Instead, the next pick **must be from `DANCE_POSITION["open"]`**, **excluding** fast-paced dances.

4. **Handling Exhaustion of Non-Fast-Paced Dances**
   - If all valid subgenres in `DANCE_POSITION["open"]` (excluding fast-paced) are unavailable:
     - The script **allows** picking a fast-paced dance again.

---

### **3. Subgenre Selection Execution**

1. **Ensure Genre Variety**
   - If the previously picked genre is selected again, the script avoids repeating the same subgenre or a subgenre from the same group.
   - If no valid options remain, previous restrictions are lifted.

2. **Apply Fast-Paced Dance Rule**
   - If `previous_was_fast_paced = True`, restrict choices to:
     - Subgenres from `DANCE_POSITION["open"]`
     - **Excluding** `FAST_PACED_DANCES`
   - If `DANCE_POSITION["open"]` (excluding fast-paced) is exhausted, allow fast-paced dances again.

3. **Weighted Random Selection of Subgenre**
   - A subgenre is selected using `random.choices()` based on its weight.

4. **Update Tracking Variables**
   - Store the chosen subgenre and its group to prevent immediate repetition.
   - Update `previous_was_fast_paced` flag.

---

### **4. Track Selection Within a Playlist**

1. **Pick Songs from Selected Subgenre Playlist**
   - Retrieve all tracks from the selected subgenre.
   - Exclude tracks already picked.
   - Randomly select **up to 2**.
   - If fewer than 2 unpicked tracks remain, skip the subgenre.

2. **Insert Pause Track After Each Selection**
   - A predefined "Pause Track" is inserted after every batch of songs.
   - If the pause track is unavailable, this step is skipped.

---
### Requirements for smooth use of the script

1. **User must manually set the playlist (subgenre) and genre names in the variables section at the beginning of the script for proper weight and other calculations**
2. **User must make sure that enough songs exist in each playlist for more balanced end result. Six or more songs should do for each playlist but having more is always better.**
