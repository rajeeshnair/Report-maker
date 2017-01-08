========================
Report Maker
========================

------------------------------------------------
A python script to generate reports with tables
------------------------------------------------

About
======

This script was written, originally as a django app, way back in 2012 January. Some business apps I have been writing required to generate reports in both HTML and PDF formats. These reports were nothing but a collection of tables, accompanied by a page title (which in turn included company name, address, banner/logo image and a report title). reports were being shown to the user in HTML format first and then, if required, can be downloaded as PDF. To generate PDF documents from python, we have reportlab library. But first of all, we need to prepare our data tables in a format conforming to their specifications. This script was then written to ease that process.

Requirements
=============

* Python >= 2.7 or 3.4

* Reportlab >= 2.5


Installation
=============

* Copy the script, ``report_maker.py``, to some place in your python path.

Usage
======

* Create required number of data-tables using ``report_maker.DataTable``: ::

    from reportlab.lib.units import inch
    from reportlab.lib import colors

    from report_maker import DataTable

    dt1 = DataTable([1*inch, 3*inch, 1*inch, 3*inch], True)

* To each table, add rows of data and styles as required.
  
* Each row of data must be an iterable of strings or anything convertible to strings: ::

    dt1.add_row(('List of customers', '', '', ''))
    dt1.add_row(('Sl.No', 'Name', 'Age', 'Profession'))
    # Data rows can be added in one go too.
    dt1.add_rows([
      (1, 'William Shakespear', 23, 'Writer'),
      (2, 'Bernard Shaw', 32, 'Lawyer'),
    ])

* Each style declaration must follow the reportlab specification: ::

    # In general, a style declaration is of following format:
    # (STYLE_ATTR_NAME, STARTING_CELL_INDEX_AS_A_TUPLE, ENDING_INDEX_TUPLE, EXTRA_ARGS_IF_ANY)
    dt1.add_style('ALIGN', (0,0), (1,0), 'CENTER')
    dt1.add_style('SPAN', (0,0), (1,0))
    dt1.add_style('ALIGN', (0, 1), (-1,1), 'CENTER')
    dt1.add_style('INNERGRID', (0,0), (-1,-1), 0, colors.black)

* Once all data rows and required styles are added, you can pass the tables to a PdfReport instance OR to any html template.

* generating PDFs with the above tables: ::

    from report_maker import PdfReport
    pr_obj = Pdfreport('REPORT TITLE', 'COMPANY-NAME')
    pdf_doc = pr_obj.build_doc([dt1])

* Save the generated pdf to local file OR send it by mail OR return an HttpResponse attaching it.

* Rendering an html template with the above tables (from Django): ::

    {% for table in tables %}
    <table>
      {% for row in table.rows %}
      <tr>
        {% for cell in row %}
          <td>{{cell.data}}</td>
        {% endfor %}
      </tr>
      {% endfor %}
    </table>
    {% endfor %}

* Each cell in data rows is an instance of ``report_maker.DataCell``. There are many helper methods like, ``get_hspan``, ``get_col_width``, ``get_align`` etc to help us render the ables in a consistent format across both PDF and HTML. Refer to each class's docs for more details.
