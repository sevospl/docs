[Cloudplow](https://github.com/l3uddz/cloudplow) (CP) is a script created by [l3uddz](https://github.com/l3uddz) that has one main component as relates to Saltbox: it's an uploader to Rclone remote. Files are moved off local storage. With support for multiple uploaders (i.e. remote/folder pairings).

## Remote Uploader Function

As setup for Saltbox, Cloudplow uploads all the content in `/mnt/local/Media/` (see [Paths](../saltbox/basics/paths.md#cloudplow)) to your cloud storage provider (e.g. Google Drive), after the folder reaches a `200` GB size threshold, when checked every `30` minutes.

_Note: The size threshold and the check interval can be changed via steps mentioned on this page._


<details>
<summary>Google Drive Daily Upload Limit (click to expand)</summary><br />

Google Drive has a max upload limit of about 750GB per day. When this limit is reached, Google Drive will put you in a 24 hour soft ban. When Cloudplow detects this (with the phrase `Failed to copy: googleapi: Error 403: User rate limit exceeded`), uploading will be suspended for 25 hours (i.e. a 25 hour ban sleep), and upon waking up, it will resume its checking and uploading tasks. This feature is enabled by default. This method is better than running Rclone task with a bwlimit, becasuse you can just upload in bursts when the uploading resumes.

_Note: The keywords or phrases that are used to monitor the ban, and the duration of the sleep time, can be changed at any time by editing the `config.json` file._

Cloudplow can also use service accounts to upload and work around this limitation.

</details>

## Config

### Default config.json file

See [Example Cloudplow configs](../reference/cloudplow.md).

### Location

```
/opt/cloudplow/config.json
```

Note: Config changes require a restart: `sudo systemctl restart cloudplow`.

### Editing

Edit in your favorite code editor  (with json highlighting) or even a unix editor like nano. 

```
nano /opt/cloudplow/config.json
```

Note: The cloudplow config file is a JSON file.  JSON files have a particular format and syntax.  If you are unfamiliar with JSON formatting and syntax, don't edit this file until you have gained that familiarity.  Here's a [random YouTube video](https://www.youtube.com/watch?v=GpOO5iKzOmY) that will give you a ten-minute overview.

### Modify Upload Threshold and Interval

```json
    "uploader": {
        "google": {
            "check_interval": 30,
            "exclude_open_files": false,
            "max_size_gb": 200,
            "opened_excludes": [
                "/downloads/"
            ],
            "size_excludes": [
                "downloads/*"
            ]
        }
```


`"check_interval":` How often (in minutes) Cloudplow checks the size of `/mnt/local/Media`.

`"max_size_gb":` Max size (in GB) Cloudplow allows `/mnt/local/Media` to get before starting an upload task.

  - Note: `max_size_gb` is rounded up, so it is advised to have it minimum `2GB` or else it would attempt upload at each interval. Explanation below. 

    - `1GB` is basically anything in there.

    - `2GB` is at least 1GB of data.


### Plex Integration 

Cloudplow can throttle Rclone uploads during active, playing Plex streams (paused streams are ignored).

```json
  "plex": {
      "enabled": false,
      "url": "https://plex.domain.com",
      "token": "YOUR_TOKEN_HERE",
      "poll_interval": 60,
      "max_streams_before_throttle": 1,
      "rclone": {
          "throttle_speeds": {
              "0": "1000M",
              "1": "50M",
              "2": "40M",
              "3": "30M",
              "4": "20M",
              "5": "10M"
          },
          "url": "http://localhost:7949"
      }
  }
```

`enabled` - Change `false` to `true` to enable.

`url` - Your Plex URL.

`token` - Your [Plex Access Token](../reference/plex_auth_token.md).

`poll_interval` - How often (in seconds) Plex is checked for active streams.

`max_streams_before_throttle` - How many playing streams are allowed before enabling throttling.

`rclone`

  - `url` - Leave as default.

  - `throttle_speeds` - Categorized option to configure upload speeds for various stream counts (where `5` represents 5 or more streams). `M` is MB/s.

     - Format: 
    
       ```json
       "STREAM COUNT": "THROTTLED UPLOAD SPEED",
       ```

### Pushover Notifications

See [here](../reference/pushover.md#cloudplow).


### Restart

Restart Cloudplow to apply the changes to the config. 

```
sudo systemctl restart cloudplow
```


## CLI


You can run a manual Cloudplow task from anywhere by just using the `cloudplow` command. 


### Manual Upload

To start uploading right away, regardless of what the folder size is: 

```
cloudplow upload
```
