# HNS Punycode processor and Pagemaker

## 20251226

- the apps in this dir manage Handshake HNS + TLD csv export files from
  - Namebase.io ['nb' - csv files in \HNS_PUNYTAG-PAGEMAKE\csv-s\csv-nb\csv_nb-tr + csv_nb-tld, for punytag_nb_tr.py and punytag_nb.py respectively] 
  - and Bob-wallet ['bob' - csv files in \HNS_PUNYTAG-PAGEMAKE\csv-s\csv-bob\csv_bob-tr + csv_bob-tld, for punytag_bob_tr.py and punytag_bob.py respectively]
- an example processed bob_tr.csv exists, derived from hns_punytag-pagemake\csv-s\csv-bob\csv_bob-tr\hns_bob_22catchall.20251226.csv

- since the app was created [by github user @i1li], Firewallet ['fw' - csv files in \HNS_PUNYTAG-PAGEMAKE\csv-s\csv-fw] and Shakestation.io ['ss' - csv files in \HNS_PUNYTAG-PAGEMAKE\csv-s\csv-ss\csv_ss-tr + csv_ss-tld] have come into existence, and it is desired to have new facility to process export files from those sources also
- at least one example file is present in each csv dir, for each type

- generate independent apps to process ss + fw exports, based on punytag_*.py
- adapt the code, for all apps, to delineate exports by headers, so explicit filenames are not needed [allowing individual export naming for user file-identification and sorting - the files may not always be in segregated dir's]

- then, amalgamate all the apps into a gui, that will recognise the origin of picked [and/or dropped?] file[s] from their csv-headers and process each appropriately
- rename processed files with suffix `_orig` and output a new file with the required processing
- potentially include sorting the processed file[s] to sub-dir[s] according to source, with options to delete original, or move it too, or leave it in place 
  - append date [yyyymmdd] to output filename

- enable recursive search for files to process
  - match already processed files if present, to not duplicate
  - provide checkbox processing
    - have 'select all/none' available

- incorporate facility for puny2uni.py, as annother tab, similarly without need for explicit file-naming protocols if possible
  - headers may not be adequate for delineation 
    - assume uni- or puny-code naming from single column [if csv] is 'bob-tld' file
    - only accept .txt files for purely uni2puny or puny2uni actions

- have a separate tab for 'pagemaker' action
- assess how tld sorting is assessed in pagemaker.py and provide
  - should be random initially, with button to sort alphanumerically[alph-num]
  - each press of 'sort' cycles random/alph-num-up/alph-num-down
  - the sort should only show already chosen category of tld [if any]
- pagemaker should be able to make the page from any format of csv, just using the tld column [ie, nb='name' or ss='domain']
  - if providing more than one file, links should go to the appropriate target site page, nb or ss, where the site sales pages addresses are as 
    - https://shakestation.io/domain/[tld]
    - https://www.namebase.io/domains/[tld]
  - only add ss tlds if 'for_sale=TRUE'
- provide facility to update a page by 
  - adding another nb csv to include tlds
  - processing a ss csv to remove tlds if marked 'for_sale=FALSE'
  - processing a custom csv to remove nb tlds
    - col's name,sell[as TRUE/FALSE]
    - do not change any tld not listed in the csv
- enable adding personalised footer/credits files


- provide the app with with a green 'process' button, yellow 'help' button with 'howto info' and red 'exit' button
- call the app hnsell

- an example html file \html\nb-sell.html is included for reference
  - it is an edited for personal use version of pagemaker output
- derive separate footer.html + credit.html files from 'footer' and 'credits' divs in nb-sell.html, for use with the pagemaker tab

### step 2

- 'sort TLDs' button should be available on the produced .html page [not as an app button .. tho useful - sort all additions to be processed, cycling by simply alph-num up/down, and separated by import file up/down]
- puny<=>uni should not process csv at all, only accept 'list' txt file[s]
