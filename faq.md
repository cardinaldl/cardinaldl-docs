# CardinalDL FAQ

This FAQ covers common login issues and some minor things that are useful to know when using CardinalDL.

## We are human

When asking a question, creating a support ticket, requesting a demo, etc., please keep in mind we're human.  
We too eat, sleep, go to our day jobs, visit family and celebrate holidays.  
You not getting an immediate response does not mean we are ignoring you.

Sometimes our moderators, MegaSwizzy and DeepSpace, may respond quicker than we do. They don't have the same level of access or knowledge as we do, but they have been here long enough to help with most common issues. Trust their guidance to the same level as you would ours.

## Troubleshooting

If you run into issues and have tried troubleshooting on your own or following the steps in this FAQ without success, please [create a support ticket](https://www.cardinaldl.com/portal/support/new/support) or reach out to us in our [Discord server](https://discord.com/invite/N9Abke3BHj) for further assistance.

In the bug report/help ticket please include:
1. What operating system you're using (e.g., Windows 11, macOS 26/Tahoe, Ubuntu 26.04 LTS, Docker).
2. The version of CardinalDL you are using.
3. A detailed description of the issue you are experiencing. If you can provide steps to reproduce it, that would be very helpful.
4. Any relevant error messages or logs (from "Settings > Logs > Download logs").

If you don't provide them, we'll ask for the logs anyway. Best to save time and include them upfront. ;)

## Cookie exporter extensions

When a service needs a cookie, log into it in your normal browser first, then use one of these extensions to export that site's cookies and import them into CardinalDL.

- **Chrome / Edge / other Chromium browsers:** [Cookie-Editor](https://chromewebstore.google.com/detail/hlkenndednhfkekhgcdicdfddnkalmdm), [Get cookies.txt LOCALLY](https://chromewebstore.google.com/detail/cclelndahbckbenkjhflpdbgdldlbecc)
- **Firefox:** [Cookie-Editor](https://addons.mozilla.org/en-US/firefox/addon/cookie-editor/), [Get cookies.txt LOCALLY](https://addons.mozilla.org/en-US/firefox/addon/get-cookies-txt-locally/)

Other cookie exporters should work too. These are just the ones we use and recommend.  
As long as it can export in Netscape format, CardinalDL can import it.

## FAQ

- [General](#faq-general)
    - [Where do I log into a service?](#general-where-to-log-in)
    - [Browse page shows no services even though I enabled them](#general-no-services-enabled)
    - [Downloaded media appears longer than the timestamp](#general-media-longer-than-timestamp)
    - [DV hybrid option not available](#general-dv-hybrid-not-available)
    - [How to install mkvtoolnix](#general-install-mkvtoolnix)
- [Netflix](#faq-netflix)
    - [Can't download 4k content from Netflix](#netflix-4k-not-available)
    - [Can't download anything from Netflix](#netflix-cant-download-anything)
- [HBO Max](#faq-hbomax)
    - [Exported cookies from HBO Max aren't working](#hbomax-exported-cookies-not-working)
- [Peacock](#faq-peacock)
    - [Exported cookies from Peacock aren't working](#peacock-exported-cookies-not-working)

### <a id="faq-general"></a>General

#### <a id="general-where-to-log-in"></a>Where do I log into a service?

In the desktop app, go to the "Browse" page and select the service you want to log into. This will open the service's login screen where you can enter your credentials.

If you are already logged in, you will see options to search, browse, and download its content instead of a login screen.

Note that some login methods are desktop-only and may not work through Docker.  
Best practice is to log in through the desktop app, then create a settings sync key and upload your settings. Your database is client-side encrypted with that settings sync key.  
Then, from Docker, restore your settings. On the CLI you would use the `--sync-download` flag to sync your settings. You can read more about that [here](./CLI/CDL-CLI-DOCS.md#settings-sync).

#### <a id="general-no-services-enabled"></a>Browse page shows no services even though I enabled them

Your account's license does not include any of the services you have enabled. Check your account settings or contact support to ensure your account has access to the desired services.

#### <a id="general-media-longer-than-timestamp"></a>Downloaded media appears longer than the timestamp

If the downloaded video appears longer than the timestamp shown, its subtitle track may have timestamps that extend beyond the actual video duration, causing the media player to display a longer length.

To fix this, enable the timing fix under "Settings > Downloads > Subtitles > ASS/SSA processing > Timing fix".

#### <a id="general-dv-hybrid-not-available"></a>DV hybrid option not available

Look at the available streams. For the DV hybrid option to show up, CardinalDL has to pair a Dolby Vision stream with an HDR10 or HDR10+ stream that share the same aspect ratio and frame rate. If no HDR stream shares the Dolby Vision stream's aspect ratio, for example one is 16:9 and the other is a wider scope crop, the option will not be available.

Some services like Disney+ have native hybrid content, meaning they already provide a combined Dolby Vision and HDR stream. In such cases, the DV hybrid option will not be available because the content is already in its optimal format.

#### <a id="general-install-mkvtoolnix"></a>How to install mkvtoolnix

mkvtoolnix's `mkvmerge` and `mkvpropedit` are bundled in the Windows release however you need to install mkvtoolnix manually on macOS and Linux.  
Note: Make sure you install at least v99. Around versions v80-90 there was a chapters handling bug that could cause issues.

For Linux:

1. Find your distro from mkvtoolnix's [official download page](https://mkvtoolnix.download/downloads.html). In this example im using ubuntu.
2. Run the following commands. Change the `echo` values to what mkvtoolnix recommends for your distribution.

```bash
wget -O /etc/apt/keyrings/gpg-pub-moritzbunkus.gpg https://mkvtoolnix.download/gpg-pub-moritzbunkus.gpg

# change the "deb...noble main" and "deb-src...noble main" lines to match what mkvtoolnix has for your distribution
echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/gpg-pub-moritzbunkus.gpg] https://mkvtoolnix.download/ubuntu/ noble main" > /etc/apt/sources.list.d/mkvtoolnix.download.list
echo "deb-src [arch=amd64 signed-by=/etc/apt/keyrings/gpg-pub-moritzbunkus.gpg] https://mkvtoolnix.download/ubuntu/ noble main" >> /etc/apt/sources.list.d/mkvtoolnix.download.list

apt update
apt install mkvtoolnix
```

Now simply run `mkvmerge --version` to verify that mkvtoolnix has been installed correctly. If it outputs the version information, the installation was successful and CardinalDL should be able to use mkvtoolnix for its operations.

<!-- TODO: add macOS brew installation instructions here as well -->

### <a id="faq-netflix"></a>Netflix

#### <a id="netflix-4k-not-available"></a>Can't download 4k content from Netflix

To get 4k access you have to purchase the Netflix 4k add-on. If you already purchased Netflix without the 4k add-on but you would like to purchase it, open a support ticket so we can assist you with upgrading your account.

If you already have the add-on and still can't download 4k content, you have probably hit Netflix's rate limit.  
In our testing, after downloading 13 4k titles you have to wait about 70 minutes before you can download more 4k content.

Note: this is a rate limit imposed by Netflix, not CardinalDL, and definitely not Netflix detection. Hitting this rate limit will not get your account banned or flagged. You just have to wait until the limit resets.

#### <a id="netflix-cant-download-anything"></a>Can't download anything from Netflix

1. Go to your account settings ("Settings > Services > Accounts") and log out of Netflix. Close/reopen CardinalDL.
2. Open your browser and sign out of Netflix completely, then log back into your account.
3. Export your Netflix cookies in Netscape format and import them into CardinalDL.
4. Try downloading content from Netflix again.

If the issue persists, please create a support ticket or let us know in our Discord server. Make sure to provide error logs from "Settings > Logs > Download logs".

### <a id="faq-hbomax"></a>HBO Max

#### <a id="hbomax-exported-cookies-not-working"></a>Exported cookies from HBO Max aren't working

This happens because some cookie exporters don't capture the `st` cookie, which is required for HBO Max authentication.

To get that cookie into your export:
1. Go to https://api.hbomax.com/ in your browser. It will fail to load because the page doesn't exist.
2. In your cookie exporter, export the cookies like you did before.
3. Import the exported cookies into CardinalDL.

If it's still not working, open the devtools and look for the `st` cookie under the "Application > Cookies" section. Make sure you add it to the Netscape format export before importing it into CardinalDL.  
If it's not in the api.hbomax.com cookies, try looking under www.hbomax.com.

The goal is to get the `st` cookie included in your export so that CardinalDL can authenticate with HBO Max correctly.

If you still can't get the `st` cookie or continue to experience issues with HBO Max authentication, please create a support ticket or reach out to us in our Discord server for further assistance.

### <a id="faq-peacock"></a>Peacock

#### <a id="peacock-exported-cookies-not-working"></a>Exported cookies from Peacock aren't working

We need the Netscape export of your Peacock cookies taken from the profile picker page, not the browse or search pages.

1. Log into Peacock. You will be taken to the profile picker page. Don't pick any profile.
2. Open your cookie exporter and export the cookies in Netscape format.
3. Import the exported cookies into CardinalDL.

If it's still not working, open a ticket with our support team or reach out to us in our Discord server. Make sure to provide error logs from "Settings > Logs > Download logs" and mention that you are having issues with Peacock cookie exports.
