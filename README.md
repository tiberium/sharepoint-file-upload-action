# Sharepoint File Upload Github Action

Uploads one or more files (via glob) to Sharepoint site.

## Variables
The following environment variables & secrets must be defined. 

If your full Sharepoint upload path is `https://example.sharepoint.com/sites/mygreatsite/Shared%20Documents/reports/detailed`, the following would be defined:

* `host_name`
  * `'example.sharepoint.com'`
* `site_name`
  * `'mygreatsite'`
* `upload_path`
  * `'reports/detailed'`


The following will be provided to you by your Sharepoint administrator when you ask for a client ID. A reminder: _put secrets in **Settings/Security/Secrets and variables/Actions**_

* `tenant_id`
* `client_id`
* `client_secret`

You will also need to provide the file or files being sent:

* `file_path`
  * A glob; something like `file.txt` or `*.md`

The following are optional and only needed to access sharepoint intances in different clouds (ie Soverign Clouds)
The defaults provided will work for all other cases

* `login_endpoint`
  * A Hostname; something like `login.microsoftonline.us`
* `graph_endpoint`
  * A Hostname; something like `graph.microsoft.us`

The following are optional, and if set to `true` will delete the file if it already exists in the sharepoint location (before uploading).

* `pre_delete`
  * `true` or `false`

## Example action.yml

```yml
name: example-file-upload
on: workflow_dispatch
jobs:
  get_report:
    runs-on: ubuntu-latest
    steps:
      - name: Create Test File
        run: touch /tmp/foo.txt
      - name: Send to Sharepoint
        uses: cringdahl/sharepoint-file-upload-action@1.0.0
        with:
          file_path: "*.txt"
          host_name: 'your.sharepoint.com'
          site_name: 'some_site'
          upload_path: 'fake_files'
          tenant_id: ${{ secrets.SHAREPOINT_TENANT_ID }}
          client_id: ${{ secrets.SHAREPOINT_CLIENT_ID }}
          client_secret: ${{ secrets.SHAREPOINT_CLIENT_SECRET }}
```

## Local development

The action is run using the `docker` container (see `Dockerfile`).
However to run or test the action locally, you can run the Python script directly using your local `python`.
To simplify the local development, you can use `poetry` to manage the dependencies (see `pyproject.toml`).

The right Python version is defined in the `.python-version` file which is used by `pyenv` to set the local python version.

## Troubleshooting

If you are building docker image locally on MacOS/M* processor, you may need to install `libfii` as a dependency for the `cffi` sub-dependency:

```dockerfile
RUN apk add --update --no-cache \
    gcc libc-dev \
    && apk add libffi-dev
```

