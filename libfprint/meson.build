libfprint_sources = [
    'fpi-async.c',
    'fpi-assembling.c',
    'fpi-core.c',
    'fpi-data.c',
    'fpi-dev.c',
    'fpi-dev-img.c',
    'fpi-img.c',
    'fpi-ssm.c',
    'fpi-sync.c',
    'fpi-poll.c',
    'fpi-usb.c',
]

nbis_sources = [
    'nbis/bozorth3/bozorth3.c',
    'nbis/bozorth3/bz_alloc.c',
    'nbis/bozorth3/bz_drvrs.c',
    'nbis/bozorth3/bz_gbls.c',
    'nbis/bozorth3/bz_io.c',
    'nbis/bozorth3/bz_sort.c',
    'nbis/mindtct/binar.c',
    'nbis/mindtct/block.c',
    'nbis/mindtct/chaincod.c',
    'nbis/mindtct/contour.c',
    'nbis/mindtct/detect.c',
    'nbis/mindtct/dft.c',
    'nbis/mindtct/free.c',
    'nbis/mindtct/getmin.c',
    'nbis/mindtct/globals.c',
    'nbis/mindtct/imgutil.c',
    'nbis/mindtct/init.c',
    'nbis/mindtct/line.c',
    'nbis/mindtct/link.c',
    'nbis/mindtct/log.c',
    'nbis/mindtct/loop.c',
    'nbis/mindtct/maps.c',
    'nbis/mindtct/matchpat.c',
    'nbis/mindtct/minutia.c',
    'nbis/mindtct/morph.c',
    'nbis/mindtct/quality.c',
    'nbis/mindtct/remove.c',
    'nbis/mindtct/ridges.c',
    'nbis/mindtct/shape.c',
    'nbis/mindtct/sort.c',
    'nbis/mindtct/util.c',
    'nbis/mindtct/xytreps.c',
]

aeslib = false
aesx660 = false
aes3k = false
drivers_sources = []
drivers_cflags = []
foreach driver: drivers
    if driver == 'upekts'
        drivers_sources += [ 'drivers/upekts.c', 'drivers/upek_proto.c' ]
    endif
    if driver == 'upektc'
        drivers_sources += [ 'drivers/upektc.c' ]
    endif
    if driver == 'upeksonly'
        drivers_sources += [ 'drivers/upeksonly.c' ]
    endif
    if driver == 'uru4000'
        drivers_sources += [ 'drivers/uru4000.c' ]
    endif
    if driver == 'aes1610'
        drivers_sources += [ 'drivers/aes1610.c' ]
        aeslib = true
    endif
    if driver == 'aes1660'
        drivers_sources += [ 'drivers/aes1660.c' ]
        aeslib = true
        aesx660 = true
    endif
    if driver == 'aes2501'
        drivers_sources += [ 'drivers/aes2501.c' ]
        aeslib = true
    endif
    if driver == 'aes2550'
        drivers_sources += [ 'drivers/aes2550.c' ]
        aeslib = true
    endif
    if driver == 'aes2660'
        drivers_sources += [ 'drivers/aes2660.c' ]
        aeslib = true
        aesx660 = true
    endif
    if driver == 'aes3500'
        drivers_sources += [ 'drivers/aes3500.c' ]
        aeslib = true
        aes3k = true
    endif
    if driver == 'aes4000'
        drivers_sources += [ 'drivers/aes4000.c' ]
        aeslib = true
        aes3k = true
    endif
    if driver == 'vcom5s'
        drivers_sources += [ 'drivers/vcom5s.c' ]
    endif
    if driver == 'vfs101'
        drivers_sources += [ 'drivers/vfs101.c' ]
    endif
    if driver == 'vfs301'
        drivers_sources += [ 'drivers/vfs301.c', 'drivers/vfs301_proto.c' ]
    endif
    if driver == 'vfs5011'
        drivers_sources += [ 'drivers/vfs5011.c' ]
    endif
    if driver == 'upektc_img'
        drivers_sources += [ 'drivers/upektc_img.c',  'drivers/upek_proto.c' ]
    endif
    if driver == 'etes603'
        drivers_sources += [ 'drivers/etes603.c' ]
    endif
    if driver == 'vfs0050'
        drivers_sources += [ 'drivers/vfs0050.c' ]
    endif
    if driver == 'elan'
        drivers_sources += [ 'drivers/elan.c' ]
    endif
endforeach

if aeslib
    drivers_sources += [ 'drivers/aeslib.c' ]
endif
if aesx660
    drivers_sources += ['drivers/aesx660.c' ]
endif
if aes3k
    drivers_sources += ['drivers/aes3k.c' ]
endif

other_sources = []
if imaging_dep.found()
    other_sources += [ 'fpi-img-pixman.c' ]
endif

libfprint_sources += configure_file(input: 'empty_file',
                                    output: 'drivers_definitions.h',
                                    capture: true,
                                    command: [
                                      '/bin/echo',
                                      drivers_struct_list
                                    ])

libfprint_sources += configure_file(input: 'empty_file',
                                    output: 'drivers_arrays.h',
                                    capture: true,
                                    command: [
                                      '/bin/echo',
                                      drivers_primitive_array + '\n\n' + drivers_img_array
                                    ])

deps = [ mathlib_dep, glib_dep, libusb_dep, nss_dep, imaging_dep ]
libfprint = library('fprint',
                    libfprint_sources + drivers_sources + nbis_sources + other_sources,
                    soversion: soversion,
                    version: libversion,
                    c_args: common_cflags + drivers_cflags,
                    include_directories: [
                      root_inc,
                      include_directories('nbis/include'),
                    ],
                    dependencies: deps,
                    install: true)

libfprint_dep = declare_dependency(link_with: libfprint,
                                   include_directories: root_inc)

install_headers(['fprint.h'], subdir: 'libfprint')

udev_rules = executable('fprint-list-udev-rules',
                        'fprint-list-udev-rules.c',
                        include_directories: [
                          root_inc,
                        ],
                        dependencies: [ deps, libfprint_dep ],
                        install: false)

if get_option('udev_rules')
    custom_target('udev-rules',
                  output: '60-fprint-autosuspend.rules',
                  capture: true,
                  command: [ udev_rules ],
                  install: true,
                  install_dir: udev_rules_dir)
endif

supported_devices = executable('fprint-list-supported-devices',
                               'fprint-list-supported-devices.c',
                               include_directories: [
                                 root_inc,
                               ],
                               dependencies: [ deps, libfprint_dep ],
                               install: false)
