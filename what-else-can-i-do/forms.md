---
layout: page
title: Forms
parent_title: What Else Can I Do
permalink: /what-else-can-i-do/forms.html
modification_time: 2015-08-05T12:00:13+00:00
---



<p>mPDF can generate a static view of HTML forms and their elements, or (from mPDF &gt;=5.3) can produce an "Active Form".</p>
<h2>"Active" Forms</h2>
<p>Active forms can be generated which can either be printed, or the data submitted to a URI. The variable <code>$this-&gt;useActiveForms</code> should be set to <span class="smallblock">TRUE</span> either at run-time or in <span class="filename">config.php</span></p>
<h3>Compatibility &amp; Limitations</h3>
<p>Active forms have been tested with Adobe Reader 10, and Foxit 5.0 Some limitations and compatibility issues seem to be determined by the PDF reader, rather than by mPDF. This is especially true of the way the form fields are displayed, handling of non-ASCII text, and encryption.</p>
<p>Encrypted Active Forms do not work with Adobe Reader 10, but work fine in Foxit 5.0; I believe this to be due to the limited support of Adobe Reader for forms using the (old) PDF 1.4 specification i.e. mPDF.</p>
<p>Brief testing with the in-built Google Chrome PDF Reader also shows some limited functionality e.g. Submit doesn't work</p>
<p>Known limitations include:</p>
<ul>
<li>cannot save a completed form</li>
<li>cannot save or export FDF data locally</li>
<li>cannot "sign" PDF forms</li>
<li>incompatible with rotated tables, HTML headers, and "keep-with-table" (is compatible with page-break:avoid - but not if rotated - and columns)</li>
<li>cannot use SIP/SMP fonts for active form elements (ones which are subset as SIP/SMP)</li>
<li>button images cannot be vector images (SVG or WMF)</li>
<li>(active) radio buttons do not work inside a DIV fixed/absolute position (or with page-break-inside: avoid)</li>
</ul>
<h3>Creating a valid Active Form</h3>
<p>A PDF document can only contain one active form e.g. submit will work on all fields in the document.</p>
<p>The method (GET/POST) and action (URI) are set when a &lt;form ...&gt; element is parsed, and remain active unless reset by another &lt;form&gt; element. All fields will be submitted as though from one form, whether they are enclosed within a &lt;form&gt; element or not.</p>
<p>All fields should have names.</p>
<p>Field names must only contain letters, numbers, colon(:), undersore(_) or hyphen(-). This is largely as per HTML spec, but cannot contain a period(.) as this is part of PDF spec. for name hierarchies.</p>
<p>Field names should usually be unique, except for radio buttons.</p>
<p>Duplicate field names can be used (e.g. to echo the text to a field elsewhere in the document), but fields with the same name must be of the the same type, and have the same default value set.</p>
<p>Value(s) for radio buttons and checkboxes are required, and can only contain letters, numbers, colon(:), undersore(_), hyphen(-) or period(.)</p>
<p>Values in all other form fields can contain any unicode character (although obviously only win-1252 codepage if you are using core fonts only for the document). HTML entities e.g. &amp;#2046; are recommended.</p>
<p>See <a href="{{ "/html-support/html-attributes.html" | prepend: site.baseurl }}">HTML attibutes</a> for details of attributes which can be set e.g. disabled, required etc.</p>
<p>See <a href="{{ "/css-stylesheets/supported-css.html" | prepend: site.baseurl }}">supported CSS</a> for style properties which can be applied.</p>
<h3>Exporting (submitting) data</h3>
<p>Data from forms can be submitted to a URI in either HTML or XFDF format (cf. <span class="filename">config.php</span>). XFDF is a form of XML and is recommended, because of encoding issues. See <span class="filename">formsubmit.php</span> in the example folder for ideas on how to handle the submitted data.</p>
<p>NB A submitted radio button field name is doubled with an underscore i.e. "myButtonName_myButtonName"</p>
<p>If the export format is XFDF, the submitted data is always UTF-8 encoded.</p>
<p>If the export format is HTML, it is much more complicated. From a "core-fonts" only document, the submitted data uses PDFDocEncoding. (See <span class="filename">formsubmit.php</span> in the example folder for a conversion script.) But if the form contains any characters which are not in the PDFDocEncoding (similar to Windows-1252), Adobe Reader will decide which encoding to use(!?)</p>
<p>It is therefore recommend that you either use mPDF('c') and decode html POST from PDFDocEncoding, or use XFDF.</p>
<p>The default HTML submit method is POST;&nbsp; GET only seems to work from a PDF document opened in a standalone Reader (not in the browser).</p>
<p>You can specify a mailto address as a URI e.g. <code>action="mailto:email@address"</code> but you may find that it is blocked by the user's computer if using the HTML format.</p>
<h3>Radio buttons</h3>
<p>Disabled: if one radio button is set as disabled, mPDF will disable the whole group. PDF readers seem to handle this situation differently i.e. Adobe Reader 10 still allows selection of the disabled button, whilst Foxit disables the whole group.</p>
<h3>Javascript</h3>
<p>Javascript can be set for buttons using <code>onClick=""</code> but note this uses "Acrobat" Javascript. (You can download the Acrobat Javascript reference manual from the Adobe Developer's site).</p>
<p>For select, text and textarea you can use <code>onChange=""</code> which is triggered after the value has been changed.</p>

<div class="alert alert-info" role="alert"><strong>Note:</strong> From mPDF &gt;= 5.4, &lt;textarea&gt; and &lt;input type="text"&gt; will accept javascript as: <code>onKeystroke</code>, <code>onValidate</code>, <code>onCalculate</code> and <code>onFormat</code>.

<code>onChange</code> is depracated but works as <code>onCalculate</code> (for &lt;textarea&gt; and &lt;input&gt;).

Select still accepts <code>onChange</code>.</div>
<p>Unicode characters in JavaScript must be written by typing a backslash, a lowercase "u", and then the four digit hexadecimal number corresponding to the character's encoding in the utf-16 character set e.g. \u2042</p>
<h3>Appearance of form fields</h3>
<p>Adobe Reader 10 largely ignores any control one tries to place on the appearance of some form fields, and does it's own thing. In general, the <code>font-size</code> set for the form field will determine its size, and for text/textarea and select fields, <code>color</code> will determine the font colour used. CSS values for <code>border-color</code> or <code>background-color</code> will work for (non-image) buttons, textarea and text fields. Other things like the border style and width can be altered by configurable variables in <span class="filename"><code>config.php</code></span> but the level of control is disappointing.</p>
<p>Radio buttons and check-boxes use Adobe Reader's own icons, but Foxit uses information provided by the PDF file. The variable <code>$this-&gt;formUseZapD</code> determines whether ZapfDingbat symbols are used, or mPDF's appearance streams designed to mimic Adobe Reader's appearance.</p>
<p>Some components of interactive forms may be output in RGB colorspace even if you have specified <code>restrictColorSpace</code>. Since restricted colorSpace is mainly used for PDFA/PDFX files - which cannot contain active form fields anyway - this shouldn't matter.</p>