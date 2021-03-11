The theme used for the MicroPasts installation of Pybossa is built off the base
Pybossa default theme and has been customised and ported to Bootstrap 4.5
CSS framework. The theme is a mix of python, javascript, css and HTML5 and is
quite easy to adapt and extend.

## Theme development

The theme's code is version controlled on GitHub under the MicroPasts organisation
from where it is deployed directly to the server running the Pybossa instance.
It has been developed by Daniel Pett and is stored at:

https://github.com/MicroPasts/MicroPasts-pybossa-theme

It has had two releases 6 years apart.

### Installing the theme

To work on the theme, you will need to do the following:

```bash
cd pybossa/pybossa/themes
git clone https://github.com/MicroPasts/MicroPasts-pybossa-theme
```

This will install the theme into your installation. You will now need to enable it
within the system. To do this:

```bash
cd pybossa
nano settings_local.py
```

Now add:

```bash
THEME = 'MicroPasts-pybossa-theme'
```

And save your file. Restart the services and your theme should be active.

### Updating the theme

If any changes are made to the template, you will need to pull these in via git:

```bash
cd pybossa/pybossa/themes/MicroPasts-pybossa-theme
git pull
```
