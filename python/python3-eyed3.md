# eyed3

Dealing With Custom Tags
------------------------

This code erases existing custom tags and sets new ones.

TXXX is a type of list, eyed3 does weird things with attributes and unfortunately 
I could not figure out how to simply `= []` without errors.

Word of Warning. Custom tags are APPENDED to on each `tag.save()`, as such if you want to modify or rewrite a tag, 
you need to delete the existing tag and rewrite it. 

In my case and as demonstrated below, I clear all existing custom tags and rewrite my own. 

```python
import eyed3
from eyed3.id3.frames import UserTextFrame

af = eyed3.load(show.file.path)

# Clear existing tags.
if b'TXXX' in af.tag.frame_set:
    af.tag.frame_set[b'TXXX'].clear()

# Ensure TXXX exists in the frame_set. You must set at least one tag to initialize it.
if b'TXXX' not in af.tag.frame_set:
    af.tag.frame_set[b'TXXX'] = UserTextFrame(description="MY_CUSTOM_TAG", text="My Custom Data!"),

# After you've set at least one custom tag (or at least one pre-existed), 
# you can then treat it like a normal list
af.tag.frame_set[b'TXXX'].extend([
    UserTextFrame(b'TXXX', 'ORIG_TITLE', original_title),
    UserTextFrame(b'TXXX', 'ORIG_ARTIST', original_artist),
    UserTextFrame(b'TXXX', 'ORIG_ALBUM_ARTIST', original_album_artist),
])

```

