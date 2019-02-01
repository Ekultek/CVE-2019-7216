# CVE-2019-7216

In `filechucker.cgi` version 5.09 and `filechucker_prefs.cgi` version 4.99e-free-e02 there is a filter bypass, upon upload, that can lead to remote code execution providing that the user uploads a malicious file.

The bypass occurs when `filechucker` attempts to filter out the file extension and test against `filechucker_prefs` configuration of non-acceptable extensions:

```bash
$PREF{only_allow_these_file_extensions}			= '';
$PREF{disallow_these_file_extensions}			= '.exe .php .php3 .php4 .php5 .phtml .cgi .pl .sh .py .js .htaccess .htpasswd .cmd .bat .ftpquota';
$PREF{allow_files_without_extensions}			= 'yes';
$PREF{disallow_these_strings_within_filenames}		= '\.php \.asp \.cgi \.pl$ \.plx torrent dvdrip'; # can include regexes, so periods must be escaped.  must be in single quotes.#
```

The above is the default configuration when first downloaded. as you can see certain file extensions are not allowed. The bypass occurs when you append one of three things to the file name:

 - `.%%<EXT>`
 - `.%<EX>%<T>`
 - `%2E.%<EXT>`

The first and third _do not always work_ the second is almost guaranteed to work successfully. For example if we have the filename `491f21bfbe8fc71eafc9ed76565b0bb4.%ph%p` once filtered through the above the filename will be uploaded as `491f21bfbe8fc71eafc9ed76565b0bb4.php`.

# Dorks

There are a couple Google dorks to allow you to find the `filechucker.cgi` file.

 - `inurl:filechucker.cgi`
 - `inurl:/cgi-bin/filechucker`

# Proof Of Concept

The below video is a proof of concept taken on encodables website (the demo):

[![to_video](https://user-images.githubusercontent.com/14183473/52137309-dfa4df80-260f-11e9-89bf-93b0f75d09f2.png)](https://vimeo.com/314260762)

