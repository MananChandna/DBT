
  <h1>dbt + BigQuery Retail Analytics Demo</h1>
  <p>This mini-project demonstrates dbt Cloud batch/incremental transformations on a retail dataset (<em>customers, orders, products</em>) using Google BigQuery and GitHub. Includes model, test, docs, and lineage best practices.</p>

  <h2>Key Features</h2>
  <ul>
    <li>dbt Cloud with BigQuery warehouse (repo versioned on GitHub)</li>
    <li>Batch and incremental models with <code>ref()</code> and <code>source()</code> logic</li>
    <li>Automated tests (<code>not_null</code>, <code>unique</code>) in YAML</li>
    <li>Clean documentation and lineage (<code>dbt docs</code> web UI)</li>
  </ul>

  <h2>Quickstart</h2>
  <ol>
    <li>Clone repo and link in dbt Cloud</li>
    <li>Connect BigQuery (raw.orders, raw.customers, raw.products) using service account JSON as in the YouTube tutorial</li>
    <li>Run core commands:<br>
      <code>dbt run</code><br>
      <code>dbt test</code><br>
      <code>dbt docs generate</code><br>
      <code>dbt docs serve</code>
    </li>
    <li>Explore docs and DAG at <code>localhost:8000</code> or in dbt Cloud</li>
  </ol>

  <h2>Model (Incremental)</h2>
  <pre class="example"><code>{{ config(materialized='incremental', unique_key='order_id') }}
SELECT
  order_id,
  customer_id,
  SUM(total_price) AS total_order_amount,
  MAX(order_date) AS latest_order_date
FROM
  {{ source('raw', 'orders') }}
{% if is_incremental() %}
  WHERE order_date &gt; (SELECT MAX(latest_order_date) FROM {{ this }})
{% endif %}
GROUP BY order_id, customer_id
</code></pre>

  <h2>YAML Test/Doc Example</h2>
  <pre class="example"><code>models:
  - name: customer_order_summary_incremental
    description: "Incremental aggregation of order values and dates."
    columns:
      - name: order_id
        description: "Unique order identifier."
        tests: [not_null, unique]
      - name: customer_id
        description: "Customer reference."
        tests: [not_null]
</code></pre>

  <h2>Reference &amp; Credits</h2>
    <ul>
      <li>Based on <a href="https://youtu.be/C6BNAfaeqXY" target="_blank">this dbt + BigQuery YouTube tutorial</a></li>
      <li>Data: customers, orders, products (demo only)</li>
    </ul>
</body>
</html>
