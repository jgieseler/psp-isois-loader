psp-isois-loader
================

Python data loader for PSP/ISOIS instrument. At the moment provides released data obtained by SunPy through CDF files from CDAWeb for the following datasets:

- PSP_ISOIS-EPIHI_L2-HET-RATES60: Parker Solar Probe ISOIS EPI-Hi Level 2 HET 1-minute Rates (`Info <https://cdaweb.gsfc.nasa.gov/misc/NotesP.html#PSP_ISOIS-EPIHI_L2-HET-RATES60>`_, `Metadata <https://cdaweb.gsfc.nasa.gov/pub/software/cdawlib/0SKELTABLES/psp_isois-epihi_l2-het-rates60_00000000_v01.skt>`_)
- PSP_ISOIS-EPIHI_L2-HET-RATES3600: Parker Solar Probe ISOIS EPI-Hi Level 2 HET Hourly Rates (`Info <https://cdaweb.gsfc.nasa.gov/misc/NotesP.html#PSP_ISOIS-EPIHI_L2-HET-RATES3600>`_, `Metadata <https://cdaweb.gsfc.nasa.gov/pub/software/cdawlib/0SKELTABLES/psp_isois-epihi_l2-het-rates3600_00000000_v01.skt>`_) (higher coverage than 'RATES60' before mid-2021)
- PSP_ISOIS-EPILO_L2-PE: Parker Solar Probe ISOIS EPI-Lo Level 2 Particle Energy (`Info <https://cdaweb.gsfc.nasa.gov/misc/NotesP.html#PSP_ISOIS-EPILO_L2-PE>`_, `Metadata <https://cdaweb.gsfc.nasa.gov/pub/software/cdawlib/0SKELTABLES/psp_isois-epilo_l2-pe_00000000_v01.skt>`_)


Disclaimer
----------
This software is provided "as is", with no guarantee. It is no official data source, and not officially endorsed by the corresponding instrument teams. Please always refer to `the instrument/data descriptions <https://spp-isois.sr.unh.edu/>`_ before using the data!

Caveats
-------
- A lot of PSP/ISOIS datasets are not supported at the moment, for example:
 - PSP_ISOIS-EPIHI_L2-LET1-RATES60
 - PSP_ISOIS-EPIHI_L2-LET2-RATES60
 - PSP_ISOIS-EPILO_L2-IC
- For EPIHI, energy values are only loaded from the first day of the interval! (For EPILO, energy values are the mean of the whole loaded interval.)
- EPILO energy tables changed on June 14, 2021


Installation
------------

psp_isois_loader requires python >= 3.8 and SunPy >= 4.0.0

It can be installed from this repository using pip:

.. code:: bash

    pip install git+https://github.com/jgieseler/psp-isois-loader

**Note:** Windows users might `need to install git <https://github.com/git-guides/install-git>`_ for this to work!

Usage
-----

The standard usecase is to utilize the ``psp_isois_load`` function, which
returns Pandas dataframe(s) of the PSP/ISOIS measurements.

.. code:: python

   from psp_isois_loader import psp_isois_load
   import datetime as dt

   df, meta = psp_isois_load(dataset="PSP_ISOIS-EPILO_L2-PE",
                           startdate=dt.datetime(2021, 4, 16),
                           enddate="2021/04/20",
                           resample="1min",
                           path=None,
                           epilo_channel='F',
                           epilo_threshold=None)

Input
~~~~~

-  ``dataset``: (see above for explanation)
 - ``'PSP_ISOIS-EPIHI_L2-HET-RATES60'``
 - ``'PSP_ISOIS-EPIHI_L2-HET-RATES3600'`` (higher coverage than ``'RATES60'`` before mid-2021)
 - ``'PSP_ISOIS-EPIHI_L2-LET1-RATES60'`` (not yet supported)
 - ``'PSP_ISOIS-EPIHI_L2-LET2-RATES60'`` (not yet supported)
 - ``'PSP_ISOIS-EPILO_L2-PE'``
 - ``'PSP_ISOIS-EPILO_L2-IC'`` (not yet supported)
-  ``startdate``, ``enddate``: datetime object or "standard" datetime string
-  ``resample``: Pandas frequency (e.g., ``'1min'`` or ``'1h'``), or ``None``, optional. Frequency to which the original data (~24 seconds) is resamepled. By default ``'1min'``.
-  ``path``: String, optional. Local path for storing downloaded data, e.g. ``path='data/psp/isois/'``. By default `None`. Default setting saves data according to `sunpy's Fido standards <https://docs.sunpy.org/en/stable/guide/acquiring_data/fido.html#downloading-data>`_. The default setting can be changed according to the corresponding `sunpy documentation <https://docs.sunpy.org/en/stable/guide/customization.html>`_, where the setting that needs to be changed is named ``download_dir`` (e.g., one could set it to a shared directory on a multi-user system).
-  ``epilo_channel``: String, optional. Only used for EPILO data. Channel of EPILO: 'E', 'F', or 'G'. By default 'F'.
-  ``epilo_threshold``: Integer or float, optional. Only used for EPILO data. Replace all flux/countrate values in ``df`` above ``epilo_threshold`` with ``np.nan``, by default ``None``.
      

Return
~~~~~~

-  Pandas data frame, optional multiindex for pitch-angle resolved fluxes. Energies are given in ``eV``, differential intensities in ``cm-2 s-1 sr-1 eV-1``. See info links above for the different datasets for a description of the dataframe columns.
-  Dictionary of metadata (e.g., energy channels). NOTE: For EPIHI energy values are only loaded from the first day of the interval! For EPILO energy values are the mean of the whole loaded interval.


Data folder structure
---------------------

If no ``path`` argument is provided, all data files are automatically saved in a SunPy subfolder of the current user home directory.


Flux value threshold
--------------------

If a flux/countrate ``epilo_threshold`` is defined (as integer or float), all fluxes above this value will be replaced with ``np.nan``. This might me useful if there are some 'outlier' data points. For example, see the following two figures for ``threshold=None`` and ``threshold=1000``, respectively:

|psp_isois_epilo_org|
|psp_isois_epilo_threshold|

.. |psp_isois_epilo_org| image:: https://github.com/jgieseler/psp-isois-loader/raw/main/docs/psp_isois_epilo_org.png
.. |psp_isois_epilo_threshold| image:: https://github.com/jgieseler/psp-isois-loader/raw/main/docs/psp_isois_epilo_threshold.png

License
-------

This project is Copyright (c) Jan Gieseler and licensed under
the terms of the BSD 3-clause license. This package is based upon
the `Openastronomy packaging guide <https://github.com/OpenAstronomy/packaging-guide>`_
which is licensed under the BSD 3-clause license. See the licenses folder for
more information.

Acknowledgements
----------------

The development of this software has received funding from the European Union's Horizon 2020 research and innovation programme under grant agreement No 101004159 (SERPENTINE).
