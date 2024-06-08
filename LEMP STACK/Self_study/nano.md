# Nano Editor Documentation and Basic Commands

__Nano is a powerful yet user-friendly text editor pre-installed on most Unix-based systems. It caters to both novice and experienced users with its intuitive interface and fundamental editing functionalities. Unlike complex editors like Vim or Emacs, Nano offers a smooth learning curve, making it ideal for quick edits and basic text manipulations.__


### Opening Files

To open an existing file for editing, use the following command in the terminal:

```
nano filename
```
Replace filename with the actual name of the file to be accessed. If the file doesn't exist, Nano will create a new one with the specified name.

### Saving Changes

After making edits, save the work by pressing
```
Ctrl + O
```
There'll be a prompt to confirm the filename. Press Enter to keep the existing name or type a new one, followed by Enter to save.

### Exiting Nano

To exit Nano, press
```
Ctrl + X
```
If changes have not been saved, Nano will prompt for confirmation before closing.


### Basic Navigation

- __Cursor Movement:__ Utilize the arrow keys ```(Up, Down, Left, Right)``` to navigate within the text.

- __Page Scrolling:__ Move down a page with ```Ctrl+V``` and up a page with ```Ctrl+Y```.

### Editing Text

- __Inserting Text:__ Simply start typing at your desired location within the file.

- __Deleting Text:__ Employ the ```Backspace or Delete``` key to remove characters.

__Note:__ While Nano lacks built-in cut, copy, and paste functionalities like some editors, one can leverage the terminal's standard commands or mouse actions to achieve these operations.

### Searching for Text

To initiate a search, press ```Ctrl+W```. Enter the text you want to locate and press ```Enter``` to begin the search.

### Additional Considerations

- __Undo:__ Unlike conventional editors, Nano doesn't offer a complete undo feature. However, the ```Alt+U``` shortcut allows you to undo the most recent action.

- __Redo:__ Nano currently lacks a ```built-in``` redo functionality.


### Conclusion

Nano serves as a versatile text editor, providing the core functionalities needed for editing text within the terminal environment. Its user-friendly interface and straightforward commands make it an excellent choice for beginners and experienced users alike. As you gain familiarity with Nano, you may discover additional features and shortcuts to further enhance your editing workflow.