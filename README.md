# Plex Media Analyzer
Have you ever wondered how many hours you've spent watching movies or tv shows? If you use
Plex for all your media, well then you're in luck. This code can you help you find some 
interesting statistics on your Plex Media Server usage.

So far the code will tell you:
* How many shows you have
* How many episodes you have (for each show and in total)
* How many episodes you have watched
* How many hours of content you have (episodes)
* How many hours of content you have watched (episodes)
* How many movies you have
* How many movies you have watched
* How many hours of content you have watched (movies)

## Further additions
Wanna get some additional data from Plex? Add an issue and let me know

## Disclaimer
The code in this repository is generally just proof of concept.
I provide no guarentee that it works for you. Use the code in this repository at your own risk



## Usage
**Please note** that this code will not work if your Plex Media Server is password protected.

Get the latest copy of the source by cloning the repository

	git clone git@github.com:ksmandersen/Plex-Media-Analyzer.git
	
Navigate to the cloned directory and edit the following line in the ```Analyzer``` file

	define('PLEX_URL', 'http://127.0.0.1:32400');
	
Fill in the IP address or the hostname of your Plex Media Server.
Open up your command line and navigate to the cloned directory and run the following command:

	chmod u+x Analyzer
	
To run the Media Analyzer just type
	
	./Analyzer
	
If you wan't a more verbose output pass ```-v``` or ```--verbose``` along.

## License
This is free and unencumbered software released into the public domain.

Anyone is free to copy, modify, publish, use, compile, sell, or
distribute this software, either in source code form or as a compiled
binary, for any purpose, commercial or non-commercial, and by any
means.

In jurisdictions that recognize copyright laws, the author or authors
of this software dedicate any and all copyright interest in the
software to the public domain. We make this dedication for the benefit
of the public at large and to the detriment of our heirs and
successors. We intend this dedication to be an overt act of
relinquishment in perpetuity of all present and future rights to this
software under copyright law.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS BE LIABLE FOR ANY CLAIM, DAMAGES OR
OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE,
ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
OTHER DEALINGS IN THE SOFTWARE.

For more information, please refer to [http://unlicense.org/]()