#!/usr/local/bin/blaze bash

# Notflix

```sh
while getopts "ms" arg ; do
    case $arg in
        m)
			miniflix=true
            ;;
        s)
			silent=true
            ;;
    esac
done

shift $((OPTIND-1))
OTHERARGS=$@

BASE_URL="https://1337x.torrentbay.net/"

query=$(printf '%s' "$OTHERARGS" | tr ' ' '+' )
if [ "$miniflix" = true ] ; then
	page=$(curl -s "$BASE_URL"sort-search/"$query"/size/asc/1/)
else
	page=$(curl -s "$BASE_URL"srch?search="$query")
fi
movie=$(echo $page | grep -Eo "torrent\/[0-9]*\/[a-zA-Z0-9?%-_]*\/" | head -n 1)
magnet=$(curl -s https://1337x.torrentbay.net/$movie | grep -Pom 1 "magnet[^\"]*")
echo $magnet

if [ "$silent" != true ] ; then
	title=$(echo "$page" | grep "$movie" | grep -Po "(?<=\">).+?(?=</a>)" | tail -n 1)
	seeders=$(echo "$page" | grep -1 "$movie" | tail -n 1 | grep -Po "(?<=\>)[0-9]+(?=\<)")
	leechers=$(echo "$page" | grep -2 "$movie" | tail -n 1 | grep -Po "(?<=\>)[0-9]+(?=\<)")
	size=$(echo "$page" | grep -4 "$movie" | tail -n 1 | grep -o "[0-9]\+\.[0-9] [KMG]B")
	echo
	echo -e "\e[1;97m""$title""\e[0m"
	echo -e "$size		\e[32mS:$seeders	\e[31mL:$leechers\e[0m"
fi
```
