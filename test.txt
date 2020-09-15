#!/usr/bin/env bash

sourceFile=${1:-"$(find / -name "*.c" -print -quit 2>/dev/null)"}
source="$(cat "$sourceFile")"
index=0
line=0
lines="$(tput lines)"

isMac="$(uname | grep -c "Darwin")"

if [ "$isMac" -eq 1 ]; then
  color="$(osascript -e "tell application \"Terminal\" to get background color of window 1")"
  osascript -e "tell application \"Terminal\" to set background color of window 1 to {0,0,0,0}"
fi

ctrla=$(echo -e '\001\c')
ctrlb=$(echo -e '\002\c')

function printMenu {
  tput cup "$lines" 0
  tput setaf 0
  tput setab 2
  echo -n "Ctrl+A"
  tput sgr0
  tput setaf 2
  echo -ne " Buy Bitcoin\t"
  tput setaf 0
  tput setab 2
  echo -n "Ctrl+B"
  tput sgr0
  tput setaf 2
  echo -n " Buy Lightcoin"
  tput cup 0 0

  tput setaf 2
}

function printSection {
  output="${source:$index:3}"
  echo -ne "$output"
  if [ "$(echo -ne "$output" | tr '\n' '' | grep -c '')" -gt 0 ]; then
    line=$((line+1))
    if [ $line -gt 14 ]; then
      clear
      line=0
      printMenu
    fi
  fi
  index=$((index+3))
}

function finish {
  if [ "$isMac" -eq 1 ]; then
    osascript -e "tell application \"Terminal\" to set background color of window 1 to {$color}"
  fi
  clear
}
trap finish EXIT

function goto {
  if [ "$isMac" -eq 1 ]; then
    open "$1"
  fi
}

clear

printMenu

while true; do
  read -sN 1 entry
  case $entry in
    $ctrla) goto "http://google.com/";;
    $ctrlb) goto "http://google.com/";;
    *) printSection;;
  esac
done
