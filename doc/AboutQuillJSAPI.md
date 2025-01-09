Upgrading to 2.0
Quill has been significantly modernized. Leveraging the latest browser-supported APIs, Quill now delivers a more efficient and reliable editing experience.

Quill
The Quill repository has been rewritten in TypeScript, providing an official TypeScript definition file.

If you have @types/quill installed, uninstall it, as it is no longer needed
SVG icons are now inlined in the source code, eliminating the need to set up loaders for .svg files in your bundler.
Options
strict removed

Previously some changes that were small in practice (renames) but would warrant a semver major bump would be hidden under this configuration. This ended up being more confusing than helpful as we will no longer make use of this.

registry - added to allow multiple editors with different formats to coexist on the same page. Learn more.

scrollingContainer removed

Quill will now automatically detect the scrollable ancestor, eliminating the need to provide this option. This new behavior is more robust and works seamlessly with nested scrollable elements.

Clipboard
convert - API changed to include both HTML and text and previous functionality is broken into multiple method calls (convert, onCapturePaste) to allow more surface to hook into.
onCapturePaste - Added
Configuration
matchVisual removed - Previously there was a choice between using visual or semantic interpretation of pasted whitespace; now just the semantic interpretation is used. Visual matching was expensive, requiring the DOM renderer which is no longer available in the new clipboard rewrite.
pasteHTML removed - Deprecated alias to dangerouslyPasteHTML.
Keyboard
Binding key is no longer case insensitive. To support bindings like key: '@', modifiers are taken into account so the shift modifier will affect case sensitivity.
Binding key now supports an array of keys to easily bind to multiple shortcuts.
Native keyboard event object is now also passed into handlers.
Parchment
All lists use <ol> instead of both <ul> and <ol> allowing better nesting between the two. Copied content will generate the correct semantic markup for paste into other applications.

Code block markup now uses <div> to better support syntax highlighting.

Static register method added to allow dependent chains of registration.

Static formats method now passes in scroll.

Blot constructor now requires scroll to be passed in.

Attributors are exported as top-level classes.

Instead of accessing class attributor via Parchment.Attributor.Class, you now use it at Parchment.ClassAttributor. Similarly, Parchment.Attributor.Style is now Parchment.StyleAttributor, and Parchment.Attributor.Attribute is now Parchment.Attributor.

Exports are using full names.

Instead of Parchment.Scroll, you now use Parchment.ScrollBlot. The similar change applies to Parchment.Embed, Parchment.Text, Parchment.Block, Parchment.Inline, and more.

Delta
Support for the deprecated delta format, where embeds had integer values and list attributes had different keys, is now removed
Browser
Internet Explorer support is dropped.

url for configuration: https://quilljs.com/docs/configuration

# API

Content
deleteText
getContents
getLength
getText
getSemanticHTML
insertEmbed
insertText
setContents
setText
updateContents
Formatting
format
formatLine
formatText
getFormat
removeFormat
Selection
getBounds
getSelection
setSelection
scrollSelectionIntoView
Editor
blur
focus
disable
enable
hasFocus
update
scrollRectIntoView
Events
text-change
selection-change
editor-change
off
on
once
Model
find
getIndex
getLeaf
getLine
getLines
Extension
debug
import
register
addContainer
getModule
Content
deleteText
Deletes text from the editor, returning a Delta representing the change. Source may be "user", "api", or "silent". Calls where the source is "user" when the editor is disabled are ignored.

Methods

deleteText(index: number, length: number, source: string = 'api'): Delta
Example 1

quill.deleteText(6, 4);
Example 2

const quill = new Quill('#editor', { theme: 'snow' });

document.querySelector('#deleteButton').addEventListener('click', () => {
  quill.deleteText(5, 7);
});
getContents
Retrieves contents of the editor, with formatting data, represented by a Delta object.

Methods

getContents(index: number = 0, length: number = remaining): Delta
Examples

const delta = quill.getContents();
getLength
Retrieves the length of the editor contents. Note even when Quill is empty, there is still a blank line represented by '\n', so getLength will return 1.

Methods

getLength(): number
Examples

const length = quill.getLength();
getText
Retrieves the string contents of the editor. Non-string content are omitted, so the returned string's length may be shorter than the editor's as returned by getLength. Note even when Quill is empty, there is still a blank line in the editor, so in these cases getText will return '\n'.

The length parameter defaults to the length of the remaining document.

Methods

getText(index: number = 0, length: number = remaining): string
Examples

const text = quill.getText(0, 10);
getSemanticHTML
Get the HTML representation of the editor contents. This method is useful for exporting the contents of the editor in a format that can be used in other applications.

The length parameter defaults to the length of the remaining document.

Methods

getSemanticHTML(index: number = 0, length: number = remaining): string
Examples

const html = quill.getSemanticHTML(0, 10);
insertEmbed
Insert embedded content into the editor, returning a Delta representing the change. Source may be "user", "api", or "silent". Calls where the source is "user" when the editor is disabled are ignored.

Methods

insertEmbed(index: number, type: string, value: any, source: string = 'api'): Delta
Examples

quill.insertEmbed(10, 'image', 'https://quilljs.com/images/cloud.png');
insertText
Inserts text into the editor, optionally with a specified format or multiple formats. Returns a Delta representing the change. Source may be "user", "api", or "silent". Calls where the source is "user" when the editor is disabled are ignored.

Methods

insertText(index: number, text: string, source: string = 'api'): Delta
insertText(index: number, text: string, format: string, value: any,
           source: string = 'api'): Delta
insertText(index: number, text: string, formats: { [name: string]: any },
           source: string = 'api'): Delta
Examples

quill.insertText(0, 'Hello', 'bold', true);

quill.insertText(5, 'Quill', {
  color: '#ffff00',
  italic: true,
});
setContents
Overwrites editor with given contents. Contents should end with a newline. Returns a Delta representing the change. This will be the same as the Delta passed in, if given Delta had no invalid operations. Source may be "user", "api", or "silent". Calls where the source is "user" when the editor is disabled are ignored.

Methods

setContents(delta: Delta, source: string = 'api'): Delta
Examples

quill.setContents([
  { insert: 'Hello ' },
  { insert: 'World!', attributes: { bold: true } },
  { insert: '\n' },
]);
setText
Sets contents of editor with given text, returning a Delta representing the change. Note Quill documents must end with a newline so one will be added for you if omitted. Source may be "user", "api", or "silent". Calls where the source is "user" when the editor is disabled are ignored.

Methods

setText(text: string, source: string = 'api'): Delta
Examples

quill.setText('Hello\n');
updateContents
Applies Delta to editor contents, returning a Delta representing the change. These Deltas will be the same if the Delta passed in had no invalid operations. Source may be "user", "api", or "silent". Calls where the source is "user" when the editor is disabled are ignored.

Methods

updateContents(delta: Delta, source: string = 'api'): Delta
Examples

// Assuming editor currently contains [{ insert: 'Hello World!' }]
quill.updateContents(new Delta()
  .retain(6)                  // Keep 'Hello '
  .delete(5)                  // 'World' is deleted
  .insert('Quill')
  .retain(1, { bold: true })  // Apply bold to exclamation mark
);
// Editor should now be [
//  { insert: 'Hello Quill' },
//  { insert: '!', attributes: { bold: true} }
// ]
Note
This method updates the contents from the beginning, not from the current selection. Use Delta#retain(length: number) to skip the contents you wish to leave unchanged.

Formatting
format
Format text at user's current selection, returning a Delta representing the change. If the user's selection length is 0, i.e. it is a cursor, the format will be set active, so the next character the user types will have that formatting. Source may be "user", "api", or "silent". Calls where the source is "user" when the editor is disabled are ignored.

Methods

format(name: string, value: any, source: string = 'api'): Delta
Examples

quill.format('color', 'red');
quill.format('align', 'right');
formatLine
Formats all lines in given range, returning a Delta representing the change. See formats for a list of available formats. Has no effect when called with inline formats. To remove formatting, pass false for the value argument. The user's selection may not be preserved. Source may be "user", "api", or "silent". Calls where the source is "user" when the editor is disabled are ignored.

Methods

formatLine(index: number, length: number, source: string = 'api'): Delta
formatLine(index: number, length: number, format: string, value: any,
           source: string = 'api'): Delta
formatLine(index: number, length: number, formats: { [name: string]: any },
           source: string = 'api'): Delta
Examples

quill.setText('Hello\nWorld!\n');

quill.formatLine(1, 2, 'align', 'right');   // right aligns the first line
quill.formatLine(4, 4, 'align', 'center');  // center aligns both lines
formatText
Formats text in the editor, returning a Delta representing the change. For line level formats, such as text alignment, target the newline character or use the formatLine helper. See formats for a list of available formats. To remove formatting, pass false for the value argument. The user's selection may not be preserved. Source may be "user", "api", or "silent". Calls where the source is "user" when the editor is disabled are ignored.

Methods

formatText(index: number, length: number, source: string = 'api'): Delta
formatText(index: number, length: number, format: string, value: any,
           source: string = 'api'): Delta
formatText(index: number, length: number, formats: { [name: string]: any },
           source: string = 'api'): Delta
Examples

quill.setText('Hello\nWorld!\n');

quill.formatText(0, 5, 'bold', true);      // bolds 'hello'

quill.formatText(0, 5, {                   // unbolds 'hello' and set its color to blue
  'bold': false,
  'color': 'rgb(0, 0, 255)'
});

quill.formatText(5, 1, 'align', 'right');  // right aligns the 'hello' line
getFormat
Retrieves common formatting of the text in the given range. For a format to be reported, all text within the range must have a truthy value. If there are different truthy values, an array with all truthy values will be reported. If no range is supplied, the user's current selection range is used. May be used to show which formats have been set on the cursor. If called with no arguments, the user's current selection range will be used.

Methods

getFormat(range: Range = current): Record<string, unknown>
getFormat(index: number, length: number = 0): Record<string, unknown>
Examples

quill.setText('Hello World!');
quill.formatText(0, 2, 'bold', true);
quill.formatText(1, 2, 'italic', true);
quill.getFormat(0, 2);   // { bold: true }
quill.getFormat(1, 1);   // { bold: true, italic: true }

quill.formatText(0, 2, 'color', 'red');
quill.formatText(2, 1, 'color', 'blue');
quill.getFormat(0, 3);   // { color: ['red', 'blue'] }

quill.setSelection(3);
quill.getFormat();       // { italic: true, color: 'blue' }

quill.format('strike', true);
quill.getFormat();       // { italic: true, color: 'blue', strike: true }

quill.formatLine(0, 1, 'align', 'right');
quill.getFormat();       // { italic: true, color: 'blue', strike: true,
                         //   align: 'right' }
removeFormat
Removes all formatting and embeds within given range, returning a Delta representing the change. Line formatting will be removed if any part of the line is included in the range. The user's selection may not be preserved. Source may be "user", "api", or "silent". Calls where the source is "user" when the editor is disabled are ignored.

Methods

removeFormat(index: number, length: number, source: string = 'api'): Delta
Examples

quill.setContents([
  { insert: 'Hello', { bold: true } },
  { insert: '\n', { align: 'center' } },
  { insert: { formula: 'x^2' } },
  { insert: '\n', { align: 'center' } },
  { insert: 'World', { italic: true }},
  { insert: '\n', { align: 'center' } }
]);

quill.removeFormat(3, 7);
// Editor contents are now
// [
//   { insert: 'Hel', { bold: true } },
//   { insert: 'lo\n\nWo' },
//   { insert: 'rld', { italic: true }},
//   { insert: '\n', { align: 'center' } }
// ]

Selection
getBounds
Retrieves the pixel position (relative to the editor container) and dimensions of a selection at a given location. The user's current selection need not be at that index. Useful for calculating where to place tooltips.

Methods

getBounds(index: number, length: number = 0):
  { left: number, top: number, height: number, width: number }
Examples

quill.setText('Hello\nWorld\n');
quill.getBounds(7); // Returns { height: 15, width: 0, left: 27, top: 31 }
getSelection
Retrieves the user's selection range, optionally to focus the editor first. Otherwise null may be returned if editor does not have focus.

Methods

getSelection(focus = false): { index: number, length: number }
Examples

const range = quill.getSelection();
if (range) {
  if (range.length == 0) {
    console.log('User cursor is at index', range.index);
  } else {
    const text = quill.getText(range.index, range.length);
    console.log('User has highlighted: ', text);
  }
} else {
  console.log('User cursor is not in editor');
}
setSelection
Sets user selection to given range, which will also focus the editor. Providing null as the selection range will blur the editor. Source may be "user", "api", or "silent".

Methods

setSelection(index: number, length: number = 0, source: string = 'api')
setSelection(range: { index: number, length: number },
             source: string = 'api')
Examples

quill.setSelection(0, 5);
scrollSelectionIntoView
Scroll the current selection into the visible area. If the selection is already visible, no scrolling will occur.

Note
Quill calls this method automatically when setSelection is called, unless the source is "silent".

Methods

scrollSelectionIntoView();
Examples

quill.scrollSelectionIntoView();
Editor
blur
Removes focus from the editor.

Methods

blur();
Examples

quill.blur();
disable
Shorthand for enable(false).

enable
Set ability for user to edit, via input devices like the mouse or keyboard. Does not affect capabilities of API calls, when the source is "api" or "silent".

Methods

enable(enabled: boolean = true);
Examples

quill.enable();
quill.enable(false);   // Disables user input
focus
Focuses the editor and restores its last range.

Methods

focus(options: { preventScroll?: boolean } = {});
Examples

// Focus the editor, and scroll the selection into view
quill.focus();

// Focus the editor, but don't scroll
quill.focus({ preventScroll: true });
hasFocus
Checks if editor has focus. Note focus on toolbar, tooltips, does not count as the editor.

Methods

hasFocus(): boolean
Examples

quill.hasFocus();
update
Synchronously check editor for user updates and fires events, if changes have occurred. Useful for collaborative use cases during conflict resolution requiring the latest up to date state. Source may be "user", "api", or "silent".

Methods

update(((source: string) = 'user'));
Examples

quill.update();
scrollRectIntoViewexperimental
Scrolls the editor to the given pixel position. scrollSelectionIntoView is implemented by calling this method with the bounds of the current selection.

scrollRectIntoView(bounds: {
  top: number;
  right: number;
  bottom: number;
  left: number;
});
Example 1

// Scroll the editor to reveal the range of { index: 20, length: 5 }
const bounds = this.selection.getBounds(20, 5);
if (bounds) {
  quill.scrollRectIntoView(bounds);
}
Example 2

let text = "";
for (let i = 0; i < 100; i += 1) {
  text += `line ${i + 1}\n`;
}

const quill = new Quill('#editor', { theme: 'snow' });
quill.setText(text);

const target = 'line 50';
const bounds = quill.selection.getBounds(
  text.indexOf(target),
  target.length
);
if (bounds) {
  quill.scrollRectIntoView(bounds);
}
Events
text-change
Emitted when the contents of Quill have changed. Details of the change, representation of the editor contents before the change, along with the source of the change are provided. The source will be "user" if it originates from the users. For example:

User types into the editor
User formats text using the toolbar
User uses a hotkey to undo
User uses OS spelling correction
Changes may occur through an API but as long as they originate from the user, the provided source should still be "user". For example, when a user clicks on the toolbar, technically the toolbar module calls a Quill API to effect the change. But source is still "user" since the origin of the change was the user's click.

APIs causing text to change may also be called with a "silent" source, in which case text-change will not be emitted. This is not recommended as it will likely break the undo stack and other functions that rely on a full record of text changes.

Changes to text may cause changes to the selection (ex. typing advances the cursor), however during the text-change handler, the selection is not yet updated, and native browser behavior may place it in an inconsistent state. Use selection-change or editor-change for reliable selection updates.

Callback Signature

handler(delta: Delta, oldContents: Delta, source: string)
Examples

quill.on('text-change', (delta, oldDelta, source) => {
  if (source == 'api') {
    console.log('An API call triggered this change.');
  } else if (source == 'user') {
    console.log('A user action triggered this change.');
  }
});
selection-change
Emitted when a user or API causes the selection to change, with a range representing the selection boundaries. A null range indicates selection loss (usually caused by loss of focus from the editor). You can also use this event as a focus change event by just checking if the emitted range is null or not.

APIs causing the selection to change may also be called with a "silent" source, in which case selection-change will not be emitted. This is useful if selection-change is a side effect. For example, typing causes the selection to change but would be very noisy to also emit a selection-change event on every character.

Callback Signature

handler(range: { index: number, length: number },
        oldRange: { index: number, length: number },
        source: string)
Examples

quill.on('selection-change', (range, oldRange, source) => {
  if (range) {
    if (range.length == 0) {
      console.log('User cursor is on', range.index);
    } else {
      const text = quill.getText(range.index, range.length);
      console.log('User has highlighted', text);
    }
  } else {
    console.log('Cursor not in the editor');
  }
});
editor-change
Emitted when either text-change or selection-change would be emitted, even when the source is "silent". The first parameter is the event name, either text-change or selection-change, followed by the arguments normally passed to those respective handlers.

Callback Signature

handler(name: string, ...args)
Examples

quill.on('editor-change', (eventName, ...args) => {
  if (eventName === 'text-change') {
    // args[0] will be delta
  } else if (eventName === 'selection-change') {
    // args[0] will be old range
  }
});
on
Adds event handler. See text-change or selection-change for more details on the events themselves.

Methods

on(name: string, handler: Function): Quill
Examples

quill.on('text-change', () => {
  console.log('Text change!');
});
once
Adds handler for one emission of an event. See text-change or selection-change for more details on the events themselves.

Methods

once(name: string, handler: Function): Quill
Examples

quill.once('text-change', () => {
  console.log('First text change!');
});
off
Removes event handler.

Methods

off(name: string, handler: Function): Quill
Examples

function handler() {
  console.log('Hello!');
}

quill.on('text-change', handler);
quill.off('text-change', handler);
Model
find
Static method returning the Quill or Blot instance for the given DOM node. In the latter case, passing in true for the bubble parameter will search up the given DOM's ancestors until it finds a corresponding Blot.

Methods

Quill.find(domNode: Node, bubble: boolean = false): Blot | Quill
Examples

const container = document.querySelector('#container');
const quill = new Quill(container);
console.log(Quill.find(container) === quill); // Should be true

quill.insertText(0, 'Hello', 'link', 'https://world.com');
const linkNode = document.querySelector('#container a');
const linkBlot = Quill.find(linkNode);

// Find Quill instance from a blot
console.log(Quill.find(linkBlot.scroll.domNode.parentElement));
getIndex
Returns the distance between the beginning of document to the occurrence of the given Blot.

Methods

getIndex(blot: Blot): number
Examples

let [line, offset] = quill.getLine(10);
let index = quill.getIndex(line); // index + offset should == 10
getLeaf
Returns the leaf Blot at the specified index within the document.

Methods

getLeaf(index: number): [LeafBlot | null, number]
Examples

quill.setText('Hello Good World!');
quill.formatText(6, 4, 'bold', true);

let [leaf, offset] = quill.getLeaf(7);
// leaf should be a Text Blot with value "Good"
// offset should be 1, since the returned leaf started at index 6
getLine
Returns the line Blot at the specified index within the document.

Methods

getLine(index: number): [Block | BlockEmbed | null, number]
Examples

quill.setText('Hello\nWorld!');

let [line, offset] = quill.getLine(7);
// line should be a Block Blot representing the 2nd "World!" line
// offset should be 1, since the returned line started at index 6
getLines
Returns the lines contained within the specified location.

Methods

getLines(index: number = 0, length: number = remaining): (Block | BlockEmbed)[]
getLines(range: Range): (Block | BlockEmbed)[]
Examples

quill.setText('Hello\nGood\nWorld!');
quill.formatLine(1, 1, 'list', 'bullet');

let lines = quill.getLines(2, 5);
// array with a ListItem and Block Blot,
// representing the first two lines
Extension
debug
Static method enabling logging messages at a given level: 'error', 'warn', 'log', or 'info'. Passing true is equivalent to passing 'log'. Passing false disables all messages.

Methods

Quill.debug(level: string | boolean)
Examples

Quill.debug('info');
import
Static method returning Quill library, format, module, or theme. In general the path should map exactly to Quill source code directory structure. Unless stated otherwise, modification of returned entities may break required Quill functionality and is strongly discouraged.

Methods

Quill.import(path): any
Examples

const Parchment = Quill.import('parchment');
const Delta = Quill.import('delta');

const Toolbar = Quill.import('modules/toolbar');
const Link = Quill.import('formats/link');
// Similar to ES6 syntax `import Link from 'quill/formats/link';`
Note
Don't confuse this with the import keyword for ECMAScript modules. Quill.import() doesn't load scripts over the network, it just returns the corresponding module from the Quill library without causing any side-effects.

register
Registers a module, theme, or format(s), making them available to be added to an editor. Can later be retrieved with Quill.import. Use the path prefix of 'formats/', 'modules/', or 'themes/' for registering formats, modules or themes, respectively. For formats specifically there is a shorthand to just pass in the format directly and the path will be autogenerated. Will overwrite existing definitions with the same path.

Methods

Quill.register(format: Attributor | BlotDefinintion, supressWarning: boolean = false)
Quill.register(path: string, def: any, supressWarning: boolean = false)
Quill.register(defs: { [path: string]: any }, supressWarning: boolean = false)
Examples

const Module = Quill.import('core/module');

class CustomModule extends Module {}

Quill.register('modules/custom-module', CustomModule);
Quill.register({
  'formats/custom-format': CustomFormat,
  'modules/custom-module-a': CustomModuleA,
  'modules/custom-module-b': CustomModuleB,
});

Quill.register(CustomFormat);
// You cannot do Quill.register(CustomModuleA); as CustomModuleA is not a format
addContainer
Adds and returns a container element inside the Quill container, sibling to the editor itself. By convention, Quill modules should have a class name prefixed with ql-. Optionally include a refNode where container should be inserted before.

Methods

addContainer(className: string, refNode?: Node): Element
addContainer(domNode: Node, refNode?: Node): Element
Examples

const container = quill.addContainer('ql-custom');
getModule
Retrieves a module that has been added to the editor.

Methods

getModule(name: string): any
Examples

const toolbar = quill.getModule('toolbar');