# Packaging and Maintenance Notes

This document contains maintainer notes for building, translating, and packaging Kate Quick Run.

---

## Building from Source

### Prerequisites

**Debian / Ubuntu (KDE Plasma 6):**
```bash
sudo apt install build-essential cmake extra-cmake-modules gettext \
  qt6-base-dev libkf6texteditor-dev libkf6parts-dev libkf6i18n-dev \
  libkf6iconthemes-dev libkf6service-dev libkf6xmlgui-dev \
  libkf6coreaddons-dev libkf6config-dev
```

**Arch Linux / Manjaro:**
```bash
sudo pacman -S base-devel cmake extra-cmake-modules gettext \
  ktexteditor kparts ki18n kiconthemes kservice kxmlgui kconfig \
  kcoreaddons qt6-base
```

### Build Steps

```bash
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j"$(nproc)"
sudo cmake --install build
kbuildsycoca6 --noincremental   # refresh plugin cache
```

### For Development (debug build)

```bash
cmake -S . -B build -DCMAKE_BUILD_TYPE=Debug
cmake --build build
cmake --install build --prefix ~/.local
kbuildsycoca6 --noincremental
kate &  # test with Kate
```

---

## Translations (Localization)

### Extracting Strings

To extract translatable strings from source code:

```bash
xgettext -k -k_ -kN_ kate-quickrun.cpp -o po/kate-quickrun.pot
```

This generates the **template file** (`kate-quickrun.pot`) used by translators.

### Adding a New Language

1. Create a `.po` file from the template:
   ```bash
   msginit -i po/kate-quickrun.pot -l pt_BR -o po/pt_BR/kate-quickrun.po
   ```

2. Edit the `.po` file and translate strings.

3. Submit via **KDE Translation** (Weblate): https://l10n.kde.org/

### Compiling Translations

When building, CMake automatically compiles `.po` files to `.mo` binary files.

To manually compile a translation:
```bash
msgfmt po/pt_BR/kate-quickrun.po -o po/pt_BR/kate-quickrun.mo
```

---

## Packaging for Distributions

### Debian / Ubuntu (.deb)

The `debian/` directory contains packaging metadata.

**Build a .deb package:**
```bash
sudo apt install debhelper dpkg-dev
dpkg-buildpackage -b -us -uc -tc
# output: ../kate-quickrun_*.deb
```

**Install locally:**
```bash
sudo apt install ../kate-quickrun_1.0-1_amd64.deb
```

### Arch Linux (AUR)

The `PKGBUILD` file is ready for AUR submission.

**Build locally:**
```bash
makepkg -si
```

**Submit to AUR:**
1. Create account at https://aur.archlinux.org/
2. Clone the AUR namespace:
   ```bash
   git clone ssh://aur@aur.archlinux.org/kate-quickrun.git
   cd kate-quickrun
   ```
3. Copy `PKGBUILD` and `debian/` files
4. Commit and push:
   ```bash
   git add PKGBUILD
   git commit -m "Initial commit"
   git push
   ```

### Fedora (Copr)

For Fedora/RHEL, create a Copr project:
1. Account: https://copr.fedorainfracloud.org/
2. Create project: "kate-quickrun"
3. Upload source tarball from releases

### Generic Package Metadata

**AppStream (`metainfo.xml`):**
- Already included and ready for distro use
- Installed to: `/usr/share/metainfo/org.kde.kate.quickrun.metainfo.xml`

**KDE CI (`cmake/kde-ci.yml`):**
- Configured for KDE's CI/CD pipeline
- Runs on commits and pull requests

---

## Version Numbering

We follow **Semantic Versioning** (MAJOR.MINOR.PATCH):
- `1.0.0` — first release
- `1.1.0` — new features
- `1.0.1` — bug fixes

Update version in:
1. `CMakeLists.txt` — `set(PROJECT_VERSION "1.x.x")`
2. `debian/changelog` — new entry with version
3. `CHANGELOG.md` — add release notes
4. Git tag: `git tag v1.x.x && git push --tags`

---

## Release Checklist

Before releasing a new version:

- [ ] Bump version in `CMakeLists.txt`
- [ ] Update `CHANGELOG.md` with changes
- [ ] Update `debian/changelog` with version and date
- [ ] Run full build and test: `cmake -S . -B build && cmake --build build`
- [ ] Test on Wayland and X11 (if possible)
- [ ] Commit and tag: `git tag v1.x.x`
- [ ] Push to GitHub: `git push --tags`
- [ ] Create GitHub Release with `.deb` binary
- [ ] Update translation template: `xgettext ... -o po/kate-quickrun.pot`
- [ ] Notify KDE Translation maintainers

---

## CI/CD Pipeline

### Local Testing

```bash
# Static analysis
cppcheck kate-quickrun.cpp

# Build with warnings
cmake -S . -B build -DCMAKE_CXX_FLAGS="-Wall -Wextra -pedantic"
cmake --build build
```

### KDE CI

The `.kde-ci.yml` file defines the pipeline:
- Builds on KDE's infrastructure
- Runs on commits and MRs
- Tests on multiple distros (Ubuntu, etc.)

---

## Contact & Support

- **Author:** Prof. Wyllian Bezerra da Silva — wyllianbs@gmail.com
- **GitHub:** https://github.com/wyllianbs/kate-quickrun
- **KDE Invent:** https://invent.kde.org/wyllian/kate-quickrun
- **Issues:** https://github.com/wyllianbs/kate-quickrun/issues
- **Contributing:** See [CONTRIBUTING.md](CONTRIBUTING.md)

---

*Last updated: 2026-08-05*
