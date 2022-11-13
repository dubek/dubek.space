# dubek.space

Source for generating https://dubek.space/ with Hugo.  Blog posts are under `content/posts/`.

## Build site

    hugo

## Serve site for local debugging

    hugo serve

## Development cycle

    # edit content under content/posts/
    hugo serve
    # Test locally in http://localhost:1313/

    # if OK, commit content changes
    git add content/
    git commit -m 'Add post xyz'

    # Render HTML site locally
    hugo
    git add docs
    git commit -m 'Publish'
    # Push to github, which will update github pages (AKA dubek.space) from
    # the docs directory
    git push

### Updating themes/minimo

See [instructions](https://minimo.netlify.app/docs/updating/), but largely:

    rm -rf themes/minimo
    git clone --depth 1 https://github.com/MunifTanjim/minimo themes/minimo
    git add themes/minimo
    git commit -m "Update themes/minimo"
