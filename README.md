# SocialDancePlaylistGenerator
An automated playlist generator for social dancing, designed to create well-structured dance mixes for genres like folk, ballroom, Latin, and swing. This program uses TIDAL API to fetch songs from predefined playlists, ensuring smooth transitions between tempos and dance styles while balancing fast and slow dances.

The following rules define how subgenres are selected when generating the playlist. The system ensures variety, prevents consecutive repetitions of similar styles, and balances pacing.

---

### **1. Genre Selection**

1. **Weighted Random Selection**
   - Genres are picked based on the `GENRE_WEIGHTS` dictionary.
   - Example: If "Lattarit" has a weight of 4 and "Swing" has a weight of 2, "Lattarit" is twice as likely to be picked.

2. **Avoid Consecutive Repeats**
   - The script avoids picking the same genre consecutively unless all other options are exhausted.

---

### **2. Subgenre Selection**

1. **Filter Available Subgenres**
   - Only subgenres that have unplayed tracks are considered.
   - A dictionary of `valid_subgenres` is created from `available_playlists`, ensuring:
     - The subgenre exists in the available playlists.
     - There are unplayed tracks in the subgenre.

2. **Group-Based Blocking to Prevent Consecutive Similar Picks**
   - The first word of the subgenre name is used to define its group.
   - Example:
     - "Sottiisi" and "Jenkka" are treated as the same group.
   - The script **avoids picking a subgenre from the same group twice in a row** unless no alternatives exist.

3. **Fast-Paced Dance Restrictions**
   - If the previous pick was a **fast-paced dance**, the next pick **must not** be from `FAST_PACED_DANCES`, unless no other options remain.
   - Instead, the next pick **must be from `DANCE_POSITION["open"]`**, **excluding** fast-paced dances.

4. **Handling Exhaustion of Non-Fast-Paced Dances**
   - If all subgenres in `DANCE_POSITION["open"]` (excluding fast-paced ones) are unavailable:
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
   - Exclude any tracks already picked.
   - Randomly select **up to 2 new songs**.
   - If fewer than 2 unpicked songs exist, skip the subgenre.

2. **Insert Pause Track After Each Selection**
   - A predefined "Pause Track" is inserted after every batch of songs.
   - If the pause track is unavailable, the script skips this step.

---

