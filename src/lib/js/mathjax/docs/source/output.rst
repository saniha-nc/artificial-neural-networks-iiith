.. _output-formats:

**********************
MathJax Output Formats
**********************

Currently, MathJax can render math in two ways:

- Using HTML-with-CSS to lay out the mathematics, or
- Using a browser's native MathML support.

These are implemented by the `HTML-CSS` and `NativeMML` output
processors.

If you are using one of the combined configuration files, then this will 
select one of these output processors for you.  If the config file ends in 
``_HTML``, then it is the HTML-CSS output processor, and if it ends in 
``_HTMLorMML``, then the NativeMML output processor will be chosen if the 
browser supports it, otherwise HTML-CSS output will be used.

If you are performing your own in-line or file-based configuration, you
select which one you want to use by including either ``"output/HTML-CSS"``
or ``"output/NativeMML"`` in the `jax` array of your MathJax configuration.
For example

.. code-block:: javascript

    jax: ["input/TeX","output/HTML-CSS"]

would specify TeX input and HTML-with-CSS output for the mathematics
in your document.

The HTML-CSS output processor produces high-quality output in all
major browsers, with results that are consistent across browsers and
operating systems.  This is MathJax's primary output mode.  Its major
advantage is its quality and consistency; its drawback is that it is
slower than the NativeMML mode at rendering the mathematics.  (The
HTML-CSS processor has not yet been optimized for speed, so you can
expect some improvement in the future.  Note that IE8 in "IE8
standards mode" is an order of magnitude slower than any other browser
when processing math through the HTML-CSS output processor; see
:ref:`HTML-CSS with IE8 <html-css-with-ie8>` below for some strategies
to deal with this.)

The NativeMML output processor uses the browser's internal MathML support (if
any) to render the mathematics.  Currently, Firefox has native support
for MathML, and IE has the `MathPlayer plugin
<http://www.dessci.com/en/products/mathplayer/>`_ for rendering
MathML.  Opera has some built-in support for MathML that works well
with simple equations, but fails with more complex formulas, so we
don't recommend using the NativeMML output processor with Opera.  Safari,
Chrome, Konqueror, and most other browsers don't support MathML
natively, but may in the future, since MathML is part of the HTML5 
specification.

The advantage of the NativeMML output Processor is its speed, since native
MathML support is much faster than using complicated HTML and CSS to lay
out mathematics, as the HTML-CSS output processor does.  The disadvantage
is that you are dependent on the browser's MathML implementation for your
rendering, and these vary in quality of output and completeness of
implementation.  MathJax may rely on features that are not available in
some renderers (for example, Firefox's MathML support does not implement
some of the named widths, such as ``negativethinmathspace``).  The results
using the NativeMML output processor may have spacing or other rendering
problems that are outside of MathJax's control.

Automatic Selection of the Output Processor
===========================================

Since not all browsers support MathML natively, it would be unwise to
choose the NativeMML output processor unless you are sure of your
audience's browser capabilities.  MathJax can help with that, however,
since a number of its combined configuration files will select NativeMML 
output when the browser supports it, and HTML-CSS output otherwise.  These 
are the configuration files that end in ``_HTMLorMML``.

If you are doing your own configuration, there is a special configuration
file that you can include that will choose between NativeMML and HTML-CSS
depending on the browser in use.  To invoke it, add ``"MMLorHTML.js"`` to
your configuration's `config` array, and **do not** include an output
processor in your `jax` array; MathJax will fill that in for you based on
the abilities of your user's browser.

.. code-block:: javascript

   config: ["MMLorHTML.js"],
   jax: ["input/TeX"]

You can customize which choice to make on a browser-by-browser basis
or a global basis.  See the ``config/default.js`` file or the
:ref:`Configuring MMLorHTML <configure-MMLorHTML>` section for further
details.  As an example, this configuration tells MathJax to use HTML-CSS 
output rather than native MathML support for Firefox:

.. code-block:: html

    <script type="text/x-mathjax-config">
      MathJax.Hub.Config({
        MMLorHTML: { prefer: { Firefox: "HTML" } }
      });
    </script>
    <script type="text/javascript"
      src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
    </script>

With this configuration, MathML output will be used only for IE with the
MathPlayer plugin (Firefox is the only other browser to have native MathML
support that is sufficient for use with MathJax).  Note, however, that a
user can employ the MathJax contextual menu to select the other renderer if
he or she wishes.

MathJax produces MathML that models the underlying mathematics as best
it can, rather than using complicated hacks to improve output for a
particular MathML implementation.  When you make the choice to use the
NativeMML output processor, you are making a trade-off: gaining speed
at the expense of quality and reliability, a decision that should not
be taken lightly.


.. _html-css-with-ie8:

HTML-CSS with IE8
=================

Internet Explorer 8 has at least eight different rendering modes in which
it can operate, and that are triggered by the `DOCTYPE` of the document
being viewed.  Its "quirks" mode is its fastest mode, and its "IE8
standards" mode is its slowest.  This is the mode triggered by strict HTML
document types, and since most modern content management systems now
include a `DOCTYPE` that activates "standards" mode, IE8 will operate in
its slowest manner.  This is particularly apparent when MathJax is used,
since IE8 in standards mode runs 20 to 30 times slower than it does in its
IE7 emulation mode, and 60 times slower than in quirks mode, on the sample
equations page in ``test/sample.html``.

Most users find this speed reduction unacceptable when there is much
mathematics on the page.  To overcome this problem, you may wish to
tell IE8 to use its IE7 emulation mode rather than its IE8 standards
mode.  You can accomplish this by including the line

.. code-block:: html

    <meta http-equiv="X-UA-Compatible" content="IE=EmulateIE7">

at the top of the ``<head>`` section of your HTML documents.  This
lets you keep the strict `DOCTYPE` for validation purposes, while
still managing to get reasonable performance from Internet Explorer
8.  Note that this line must come at the beginning of the ``<head>``,
before any stylesheets or other content are loaded.

Alternatively, you can use the `MMLorHTML` configuration file
described above to select NativeMML output when possible, and request
that your users install the `MathPlayer plugin
<http://www.dessci.com/en/products/mathplayer/>`_, which will render
the mathematics much more quickly.

It appears that IE9 in IE9 standards mode may perform better than IE8, but 
since IE9 is still in beta testing as of this writing, we have yet to see 
exactly what the performance of MathJax in IE9 will be like.
