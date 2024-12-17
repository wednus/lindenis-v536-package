# README

The ALSA UCM (Use Case Manager) system uses text files to record the configuration of ALSA controls for various use cases. These configuration files are stored in the `/usr/share/alsa/ucm` directory.

## alsa-ucm-aw

The `alsa-ucm-aw` package is composed of the following components: `alsa-ucm-aw-configs`, `alsa-ucm-aw-lib`, and `alsa-ucm-aw-utils`. Each serves a specific function:

### 1. `alsa-ucm-aw-configs`
Provides ALSA UCM configuration files for various platforms in Tina Linux. For more details, refer to [ucm_config.md](./ucm_config.md).

### 2. `alsa-ucm-aw-lib`
Offers a C API for applications to interact with ALSA UCM configuration files. See [interfaces.md](./interfaces.md) for further information.

### 3. `alsa-ucm-aw-utils`
Includes the `alsa-ucm-aw` application, which uses Tina ALSA UCM configuration files. For details, see [utils.md](./utils.md).

> **Note**: While the native ALSA package (`alsa-utils`) provides the `alsaucm` tool to handle UCM configurations, it has notable limitations and lacks comprehensive documentation. Hence, its use is not recommended.

## References

Documentation for ALSA UCM is scarce and lacks a complete description of its syntax and usage. The following materials were referenced during the development of `alsa-ucm-aw`, and may be helpful for those interested:

- [Use Case Interface Documentation](http://www.alsa-project.org/alsa-doc/alsa-lib/group__ucm.html)
- [Help me obtain complete documentation of ALSA UCM](http://mailman.alsa-project.org/pipermail/alsa-devel/2013-November/068763.html)
- [alsaucm should come with a man page](http://mailman.alsa-project.org/pipermail/alsa-devel/2016-December/115382.html)
- [Introduction to ALSA UCM](http://mailman.alsa-project.org/pipermail/alsa-devel/2010-November/033359.html)
- [Using UCM with PulseAudio](http://mailman.alsa-project.org/pipermail/alsa-devel/2012-June/052936.html)
- [UCM List/Set/Get Operations](http://mailman.alsa-project.org/pipermail/alsa-devel/2011-January/036280.html)
