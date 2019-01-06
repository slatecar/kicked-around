#Derived from https://github.com/evangoer/pandoc-ebook-template
#This file is licensed under the MIT License (see MIT_LICENSE.txt)
BUILD = build
BOOKNAME = Kicked_Around
TITLE = title.txt
METADATA = metadata.xml
CHAPTERS = copyright.md about.md dedication.md ch01.md ch02.md ch03.md ch04.md ch05.md ch06.md ch07.md ch08.md ch09.md ch10.md ch11.md ch12.md ch13.md ch14.md ch15.md ch16.md ch17.md ch18.md ch19.md ch20.md ch21.md ch22.md ch23.md ch24.md ch25.md epilogue.md acknowledgment.md
TOC = --toc --toc-depth=2
COVER_IMAGE = images/cover.jpg
AUDIO_VOICE = en+f2
AUDIO_SPEED = 125
AUDIO_FREQUENCY = 44100
AUDIO_BITRATE = 192k

all: clean_format book audiobook

book: clean_format txt epub mobi html pdf

clean:
	### "Cleaning build directory" ###
	if [ -d "$(BUILD)" ]; then rm -r $(BUILD); fi

clean_format:
	### "Cleanup markup format in Chapter files - BEGIN" ###
	for p in $(CHAPTERS); \
	do \
	  echo "Cleaning markup format in: " $$p ; \
	  pandoc --from=markdown --to=markdown --output=$$p $$p ; \
	done
	### "Cleanup markup format in Chapter files - END" ###

txt: clean_format $(BUILD)/txt/$(BOOKNAME).txt

epub: clean_format $(BUILD)/epub/$(BOOKNAME).epub

mobi: clean_format epub $(BUILD)/mobi/$(BOOKNAME).mobi

html: clean_format $(BUILD)/html/$(BOOKNAME).html

pdf: clean_format epub $(BUILD)/pdf/$(BOOKNAME).pdf

audiobook: clean_format txt $(BUILD)/audiobook/$(BOOKNAME).mp3

$(BUILD)/txt/$(BOOKNAME).txt: $(TITLE) $(CHAPTERS)
	### "Building txt book - BEGIN" ###
	mkdir -p $(BUILD)/txt
	echo "------------------------------------------------------------------------" > title_plain.txt
	sed G title.txt >> title_plain.txt
	pandoc $(TOC) --to=plain -o $@ title_plain.txt $^
	rm title_plain.txt
	### "Building txt book - END" ###

$(BUILD)/epub/$(BOOKNAME).epub: $(TITLE) $(CHAPTERS)
	### "Building epub book - BEGIN" ###
	mkdir -p $(BUILD)/epub
	pandoc $(TOC) -S --epub-metadata=$(METADATA) --epub-cover-image=$(COVER_IMAGE) --epub-stylesheet=style.css -o $@ $^
	### "Building epub book - END" ###

$(BUILD)/mobi/$(BOOKNAME).mobi:
	### "Building mobi book - BEGIN" ###
	mkdir -p $(BUILD)/mobi
	./kindlegen/kindlegen $(BUILD)/epub/$(BOOKNAME).epub -o $(BOOKNAME).mobi
	mv $(BUILD)/epub/$(BOOKNAME).mobi $@ $^
	### "Building mobi book - END" ###

$(BUILD)/html/$(BOOKNAME).html: $(CHAPTERS)
	### "Building html book - BEGIN" ###
	mkdir -p $(BUILD)/html
	cp $(COVER_IMAGE) $(BUILD)/html/cover.jpg
	echo "![cover](cover.jpg){#id .class width=600 height=900px}" > cover_html.md
	echo "TITLE" > title_plain.txt
	sed G title.txt >> title_plain.txt
	pandoc $(TOC) --standalone --to=html5 -o $@ title_plain.txt cover_html.md $^
	rm cover_html.md
	rm title_plain.txt
	### "Building html book - END" ###

$(BUILD)/pdf/$(BOOKNAME).pdf: $(TITLE) $(CHAPTERS)
	### "Building pdf book - BEGIN" ###
	mkdir -p $(BUILD)/pdf
	ebook-convert $(BUILD)/epub/$(BOOKNAME).epub $@
	### "Building pdf book - END" ###

$(BUILD)/audiobook/$(BOOKNAME).mp3:
	### "Building audiobook book - BEGIN" ###
	mkdir -p $(BUILD)/audiobook
	espeak -s $(AUDIO_SPEED) -v $(AUDIO_VOICE) -f $(BUILD)/txt/$(BOOKNAME).txt --stdout | ffmpeg -i - -ar $(AUDIO_FREQUENCY) -ac 2 -ab $(AUDIO_BITRATE) -f wav $(BUILD)/audiobook/$(BOOKNAME).wav
	ffmpeg -i $(BUILD)/audiobook/$(BOOKNAME).wav -codec:a libmp3lame -qscale:a 2 $@ $^
	rm $(BUILD)/audiobook/$(BOOKNAME).wav
	### "Building audiobook book - END" ###

.PHONY: clean clean_format txt epub html pdf audiobook book all

