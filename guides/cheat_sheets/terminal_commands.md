<article>
<h3>SHORTCUTS</h3>
<table>
	<tbody><tr>
		<th>Key/Command </th>
		<th>Description </th>
	</tr>
	<tr>
		<td> Tab </td>
		<td> Auto-complete files and folder names </td>
	</tr>
	<tr>
		<td> Ctrl + A </td>
		<td> Go to the beginning of the line you are currently typing on </td>
	</tr>
	<tr>
		<td> Ctrl + E </td>
		<td> Go to the end of the line you are currently typing on </td>
	</tr>
	<tr>
		<td> Ctrl + U </td>
		<td> Clear the line before the cursor </td>
	</tr>
	<tr>
		<td> Ctrl + K </td>
		<td> Clear the line after the cursor </td>
	</tr>
	<tr>
		<td> Ctrl + W </td>
		<td> Delete the word before the cursor </td>
	</tr>
	<tr>
		<td> Ctrl + T </td>
		<td> Swap the last two characters before the cursor </td>
	</tr>
	<tr>
		<td> Esc + T </td>
		<td> Swap the last two words before the cursor </td>
	</tr>
	<tr>
		<td> Ctrl + R </td>
		<td> Lets you search through previously used commands </td>
	</tr>
	<tr>
		<td> Ctrl + L or Command + K </td>
		<td> Clears the Screen </td>
	</tr>
	<tr>
		<td> Ctrl + C </td>
		<td> Kill whatever you are running </td>
	</tr>
	<tr>
		<td> Ctrl + D </td>
		<td> Exit the current shell </td>
	</tr>
</tbody></table>
<h3>CORE COMMANDS</h3>
<table>
	<tbody><tr>
		<td> cd </td>
		<td> Home directory </td>
	</tr>
	<tr>
		<td> cd [folder] </td>
		<td> Change directory </td>
	</tr>
	<tr>
		<td> cd ~ </td>
		<td> Home directory, e.g. ‘cd ~/folder/’ </td>
	</tr>
	<tr>
		<td> cd / </td>
		<td> Root of drive </td>
	</tr>
	<tr>
		<td> ls </td>
		<td> Short listing </td>
	</tr>
	<tr>
		<td> ls -l </td>
		<td> Long listing </td>
	</tr>
	<tr>
		<td> ls -a </td>
		<td> Listing incl. hidden files </td>
	</tr>
	<tr>
		<td> ls -lh </td>
		<td> Long listing with Human readable file sizes </td>
	</tr>
	<tr>
		<td> ls -R </td>
		<td> Entire content of folder recursively </td>
	</tr>
	<tr>
		<td> sudo [command] </td>
		<td> Run command with the security privileges of the superuser (Super User DO) </td>
	</tr>
	<tr>
		<td> open [file] </td>
		<td> Opens a file </td>
	</tr>
	<tr>
		<td> open . </td>
		<td> Opens the directory </td>
	</tr>
	<tr>
		<td> top </td>
		<td> Displays active processes. Press q to quit </td>
	</tr>
	<tr>
		<td> nano [file] </td>
		<td> Opens the Terminal it’s editor </td>
	</tr>
	<tr>
		<td> pico	[file] </td>
		<td> Opens the Terminal it’s editor </td>
	</tr>
	<tr>
		<td> q </td>
		<td> Exit </td>
	</tr>
	<tr>
		<td> clear </td>
		<td> Clear screen </td>
	</tr>
</tbody></table>
<h3>COMMAND HISTORY</h3>
<table>
	<tbody><tr>
		<td> history n </td>
		<td> Shows the stuff typed – add a number to limit the last n items </td>
	</tr>
	<tr>
		<td> ctrl-r </td>
		<td> Interactively search through previously typed commands </td>
	</tr>
	<tr>
		<td> ![value] </td>
		<td> Execute the last command typed that starts with ‘value’ </td>
	</tr>
	<tr>
		<td> !! </td>
		<td> Execute the last command typed </td>
	</tr>
</tbody></table>
<h3>FILE MANAGEMENT</h3>
<table>
	<tbody><tr>
		<td> touch [file] </td>
		<td> Create new file </td>
	</tr>
	<tr>
		<td> pwd </td>
		<td> Full path to working directory </td>
	</tr>
	<tr>
		<td> .. </td>
		<td> Parent/enclosing directory, e.g. </td>
	</tr>
	<tr>
		<td> ls -l .. </td>
		<td> Long listing of parent directory </td>
	</tr>
	<tr>
		<td> cd ../../ </td>
		<td> Move 2 levels up </td>
	</tr>
	<tr>
		<td> . </td>
		<td> Current folder </td>
	</tr>
	<tr>
		<td> cat </td>
		<td> Concatenate to screen </td>
	</tr>
	<tr>
		<td> rm [file] </td>
		<td> Remove a file, e.g. rm [file] [file] </td>
	</tr>
	<tr>
		<td> rm -i [file] </td>
		<td> Remove with confirmation </td>
	</tr>
	<tr>
		<td> rm -r [dir] </td>
		<td> Remove a directory and contents </td>
	</tr>
	<tr>
		<td> rm -f [file] </td>
		<td> Force removal without confirmation </td>
	</tr>
	<tr>
		<td> rm -i [file] </td>
		<td> Will display prompt before </td>
	</tr>
	<tr>
		<td> cp [file] [newfile] </td>
		<td> Copy file to file </td>
	</tr>
	<tr>
		<td> cp [file] [dir] </td>
		<td> Copy file to directory </td>
	</tr>
	<tr>
		<td> mv [file] [new filename] </td>
		<td> Move/Rename, e.g. mv -v [file] [dir] </td>
	</tr>
</tbody></table>
<h3>DIRECTORY MANAGEMENT</h3>
<table>
	<tbody><tr>
		<td> mkdir [dir] </td>
		<td> Create new directory </td>
	</tr>
	<tr>
		<td> mkdir -p [dir]/[dir] </td>
		<td> Create nested directories </td>
	</tr>
	<tr>
		<td> rmdir [dir] </td>
		<td> Remove directory ( only operates on empty directories ) </td>
	</tr>
	<tr>
		<td> rm -R [dir] </td>
		<td> Remove directory and contents </td>
	</tr>
</tbody></table>
<h3>PIPES – Allows to combine multiple commands that generate output</h3>
<table>
	<tbody><tr>
		<td> more </td>
		<td> Output content delivered in screensize chunks </td>
	</tr>
	<tr>
		<td> > [file] </td>
		<td> Push output to file, keep in mind it will get overwritten </td>
	</tr>
	<tr>
		<td> >> [file] </td>
		<td> Append output to existing file </td>
	</tr>
	<tr>
		<td> < </td>
		<td> Tell command to read content from a fi </td>
	</tr>
</tbody></table>
<h3>HELP</h3>
<table>
	<tbody><tr>
		<td> [command] -h </td>
		<td> Offers help </td>
	</tr>
	<tr>
		<td> [command] —help </td>
		<td> Offers help </td>
	</tr>
	<tr>
		<td> [command] help </td>
		<td> Offers help </td>
	</tr>
	<tr>
		<td> reset </td>
		<td> Resets the terminal display </td>
	</tr>
	<tr>
		<td> man [command] </td>
		<td> Show the help for ‘command’ </td>
	</tr>
	<tr>
		<td> whatis [command] </td>
		<td> Gives a one-line description of ‘command’ </td>
	</tr>
</tbody></table></article>
