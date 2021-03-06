.. image:: https://img.shields.io/badge/licence-AGPL--3-blue.svg
   :target: http://www.gnu.org/licenses/agpl-3.0-standalone.html
   :alt: License: AGPL-3

===========
NSCA Client
===========

This is a technical module to send passive alerts to your favorite NSCA daemon
(Nagios, Shinken...).
This module is based on the Odoo cron system and requires a NSCA client
installed on the system to satisfy the ``/usr/sbin/send_nsca`` command.

Installation
============

To use this module, you need to install a NSCA client.

On Debian/Ubuntu::

    $ sudo apt-get install nsca-client

Configuration
=============

To configure this module, you need to:

* Configure your server and a passive service in your monitoring tool
  (e.g service ``Odoo Mail Queue`` on host ``MY-SERVER``).

* Declare your NSCA server in the menu Configuration / Technical / NSCA Client / Servers

.. image:: nsca_client/static/description/server.png
   :width: 400 px

* Create NSCA checks in the menu Configuration / Technical / NSCA Client / Checks

.. image:: nsca_client/static/description/check.png
   :width: 400 px

* Code the methods which will be called by the NSCA checks.

Such methods must return a tuple ``(RC, MESSAGE, PERFORMANCE_DATA)`` where ``RC`` is an integer,
``MESSAGE`` a unicode string AND ``PERFOMANCE_DATA`` is a dictionary.
``RC`` values and the corresponding status are:

- 0: OK
- 1: WARNING
- 2: CRITICAL
- 3: UNKNOWN

``PERFORMANCE_DATA`` is not mandatory, so it could be possible to send
``(RC, MESSAGE)``.
Each element of ``PERFORMANCE_DATA`` will be a dictionary that could contain:

- value: value of the data (required)
- max: Max value on the chart
- min: Minimum value on the chart
- warn: Warning value on the chart
- crit: Critical value on the chart
- uom: Unit of Measure on the chart (s - Seconds, % - Percentage, B - Bytes, c - Continuous)

The key of the dictionary will be used as the performance_data label.

E.g:

.. code-block:: python

    class MailMail(models.Model):
        _inherit = 'mail.mail'

        @api.model
        def nsca_check_mails(self):
            mails = self.search([('state', '=', 'exception')])
            if mails:
                return (1, u"%s mails not sent" % len(mails), {
                  'exceptions': {'value': len(mails)}})
            return (0, u"OK", {'exceptions': {'value': len(mails)}})

On the example, the performance data will use the label ``exceptions`` and the
value will be the number of exception of mails.

Usage
=====

.. image:: https://odoo-community.org/website/image/ir.attachment/5784_f2813bd/datas
   :alt: Try me on Runbot
   :target: https://runbot.odoo-community.org/runbot/149/11.0

Bug Tracker
===========

Bugs are tracked on `GitHub Issues
<https://github.com/OCA/server-tools/issues>`_. In case of trouble, please
check there if your issue has already been reported. If you spotted it first,
help us smashing it by providing a detailed and welcomed feedback.


Credits
=======

Images
------

* Daniel Foré: `Icon <http://www.iconarchive.com/show/elementary-icons-by-danrabbit/Apps-system-monitor-icon.html>`_ (Elementary theme, GPL).

Contributors
------------

* Sébastien Alix <sebastien.alix@osiell.com>
* Enric Tobella <etobella@creublanca.es>

Maintainer
----------

.. image:: https://odoo-community.org/logo.png
   :alt: Odoo Community Association
   :target: https://odoo-community.org

This module is maintained by the OCA.

OCA, or the Odoo Community Association, is a nonprofit organization whose
mission is to support the collaborative development of Odoo features and
promote its widespread use.

To contribute to this module, please visit https://odoo-community.org.
