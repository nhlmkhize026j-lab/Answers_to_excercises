# Sales Report Refactoring Guide

## 1. Responsibilities Identified

The original function contains multiple responsibilities:

1.  Input validation
2.  Date filtering
3.  Custom filtering
4.  Empty data handling
5.  Metrics calculation
6.  Data grouping
7.  Report building
8.  Detailed report enrichment
9.  Forecast calculations
10. Chart generation
11. Output formatting

------------------------------------------------------------------------

## 2. Decomposition Strategy

### Layer 1: Validation

-   validate_inputs

### Layer 2: Data Preparation

-   apply_date_filter
-   apply_filters

### Layer 3: Core Logic

-   calculate_metrics
-   group_data

### Layer 4: Report Construction

-   build_base_report
-   add_grouping_to_report
-   add_detailed_data
-   add_forecast_data

### Layer 5: Visualization

-   generate_charts

### Layer 6: Output

-   format_output

------------------------------------------------------------------------

## 3. Refactored Main Function

``` python
def generate_sales_report(
    sales_data,
    report_type='summary',
    date_range=None,
    filters=None,
    grouping=None,
    include_charts=False,
    output_format='pdf'
):
    validate_inputs(sales_data, report_type, output_format)

    sales_data = apply_date_filter(sales_data, date_range)
    sales_data = apply_filters(sales_data, filters)

    if not sales_data:
        return handle_empty_data(report_type, output_format)

    metrics = calculate_metrics(sales_data)
    grouped_data = group_data(sales_data, grouping) if grouping else None

    report = build_base_report(report_type, date_range, filters, metrics)

    if grouping:
        add_grouping_to_report(report, grouped_data, metrics['total_sales'], grouping)

    if report_type == 'detailed':
        add_detailed_data(report, sales_data)

    if report_type == 'forecast':
        add_forecast_data(report, sales_data)

    if include_charts:
        report['charts'] = generate_charts(sales_data, grouped_data, grouping)

    return format_output(report, output_format, include_charts)
```

------------------------------------------------------------------------

## 4. Example Helper Functions

### Validation

``` python
def validate_inputs(sales_data, report_type, output_format):
    if not sales_data or not isinstance(sales_data, list):
        raise ValueError("Sales data must be a non-empty list")

    if report_type not in ['summary', 'detailed', 'forecast']:
        raise ValueError("Invalid report type")

    if output_format not in ['pdf', 'excel', 'html', 'json']:
        raise ValueError("Invalid output format")
```

------------------------------------------------------------------------

### Metrics Calculation

``` python
def calculate_metrics(sales_data):
    total = sum(s['amount'] for s in sales_data)
    return {
        'total_sales': total,
        'transaction_count': len(sales_data),
        'average_sale': total / len(sales_data),
        'max_sale': max(sales_data, key=lambda x: x['amount']),
        'min_sale': min(sales_data, key=lambda x: x['amount']),
    }
```

------------------------------------------------------------------------

### Output Formatter

``` python
def format_output(report_data, output_format, include_charts):
    if output_format == 'json':
        return report_data
    elif output_format == 'html':
        return _generate_html_report(report_data, include_charts)
    elif output_format == 'excel':
        return _generate_excel_report(report_data, include_charts)
    elif output_format == 'pdf':
        return _generate_pdf_report(report_data, include_charts)
```

------------------------------------------------------------------------

## 5. Key Takeaways

-   Break large functions into smaller, focused units
-   Each function should do one thing well
-   Improves readability, testing, and maintainability
-   Enables easier debugging and future extensions
