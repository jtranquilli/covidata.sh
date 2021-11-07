#/bin/bash
#julius tranquilli
#260836399

function errorMsg() {
#wrong number of argument
if [ $1  == 1 ];then

echo Wrong number of arguments
fi

#no procedure included
if [ $1 == 2 ];then
echo Procedure not provided
fi

#inputfile does not exist
if [ $1 == 3 ];then

echo Input file name does not exist

fi

}

function get() {


awk -F"," '$1 ~ x' x="$ff"  $inputFile > $outputFile

rc=$(wc -l < $outputFile)
if [ $rc == 0 ];then
echo 'rowcount,avgconf,avgdeaths,avgtests' >> $outputFile
echo 0,0,0,0 >> $outputFile
exit
fi

#ac is the average of column 7
ac=$(awk -F',' '{ sum += $7 } END { print(sum / NR) }' $outputFile)

#ad is the average of column 9

ad=$(awk -F',' '{ sum += $9 } END { print(sum / NR) }' $outputFile)

#at is the average of column 12
at=$(awk -F',' '{ sum += $12 } END { print(sum / NR) }' $outputFile)

echo 'rowcount,avgconf,avgdeaths,avgtests' >> $outputFile
echo $rc,$ac,$ad,$at >> $outputFile

}

function compare() {
#assume compFile is formatted as a get() outputFile
#append matching lines of inputFile to outputFile

awk -F"," '$1 ~ x' x="$ff"  $inputFile > $outputFile #all matching pruid lines are now in outputFile
rc=$(wc -l < $outputFile) #num of lines in the matching lines

#ac is the average of column 7
ac=$(awk -F',' '{ sum += $7 } END { print(sum / NR) }' $outputFile)

#ad is the average of column 9

ad=$(awk -F',' '{ sum += $9 } END { print(sum / NR) }' $outputFile)

#at is the average of column 12
at=$(awk -F',' '{ sum += $12 } END { print(sum / NR) }' $outputFile) #now we have all stats for matching pruid lines


#append all rows from compFile to outputfile except last two lines
head -n -2 $compFile >> $outputFile


#append inputFile stats, then compFile Stats
echo 'rowcount,avgconf,avgdeaths,avgtests' >> $outputFile
echo $rc,$ac,$ad,$at >> $outputFile #these are the inputFile stats
tail -n -1 $compFile >> $outputFile #compFile stats

echo $rc,$ac,$ad,$at >> newInvisibleFile.txt #now we append both sets of stats to a new file
tail -n -1 $compFile >> newInvisibleFile.txt

alp=$(awk -F',' 'NR==2{print $1}' newInvisibleFile.txt)
bet=$(awk -F',' 'NR==2{print $2}' newInvisibleFile.txt)
del=$(awk -F',' 'NR==2{print $3}' newInvisibleFile.txt)
eps=$(awk -F',' 'NR==2{print $4}' newInvisibleFile.txt)

rcc=$(echo "$rc $alp" | awk '{printf "%.2f \n", $1 - $2}')
acc=$(echo "$ac $bet" | awk '{printf "%.2f \n", $1 - $2}')
adc=$(echo "$ad $del" | awk '{printf "%.2f \n", $1 - $2}')
atc=$(echo "$at $eps" | awk '{printf "%.2f \n", $1 - $2}')

echo $rcc,$acc,$adc,$atc >> $outputFile

echo $(rm newInvisibleFile.txt)
}


#if the input file doesn't exist
#must support relative and absolute paths

rel=$inputFile

abs=$(pwd)/$inputFile
if [[ -f "$abs" ]] && [[ -f "$rel" ]]; then
errorMsg 3
exit
fi
if [ $# != 5 ] && [ "$1" == "compare" ]; then

errorMsg 1
exit
fi

if [ $# -lt 4 ] || [ $# -gt 7 ];then
errorMsg 1
exit
fi


if [ $# == 7 ] && [ "$1" != "get" ];then
errorMsg 2
exit
fi

if [ $# == 4 ] && [ "$1" !=  "get" ]; then
errorMsg 2

exit
fi

if [ "$1" == "get" ];then

if [ $# == 4 ];then

procedure=$1
ff=$2
inputFile=$3
outputFile=$4

get

fi fi

if [ $# == 5 ] && [ "$1" == "compare" ];then
procedure=$1
ff=$2
inputFile=$3
outputFile=$4
compFile=$5

compare



fi
