SELECT DISTINCT
	customer.customer_name AS 'Customer Name',
	oe_hdr.order_no AS 'Sales Order #',
	oe_hdr.po_no AS 'Customer PO #',
	inv_mast.item_id AS 'ISBN',
	inv_mast.item_desc AS 'Description',
	parent_oe_line.extended_desc AS 'Ext Desc',
	supplier.supplier_name AS 'Supplier Name',
	parent_oe_line.qty_ordered AS 'Qty Ordered',
	parent_oe_line.qty_canceled AS 'Qty Canceled',
	parent_oe_line.qty_invoiced AS 'Qty Invoiced',
	inv_mast.price1 AS 'List Price',
	parent_oe_line.po_cost AS 'PO Cost',
	parent_oe_line.unit_price AS 'Unit Price',
	oe_hdr.ship2_name AS 'Ship Address 1',
	oe_hdr.ship2_add1 AS 'Ship Address 2',
	oe_hdr.ship2_add2 AS 'Ship Address 3',
	oe_hdr.ship2_city AS 'Ship City',
	oe_hdr.ship2_state AS 'Ship State',
	oe_hdr.ship2_zip AS 'Ship Zip',
	oe_hdr.delivery_instructions AS 'Carrier Account #',
	freight_code.freight_cd AS 'Freight Code',
	oe_hdr.ship2_country AS 'Ship Country',
	parent_oe_line.line_no AS 'SO Ln #',
	parent_oe_line.disposition AS 'Disp',
	parent_oe_line.supplier_id AS 'Supplier ID',
	parent_oe_line.qty_allocated AS 'Qty Allocated',
	parent_oe_line.qty_on_pick_tickets AS 'Qty Picked',
	parent_oe_line.qty_staged AS 'Qty Staged',
	customer.customer_id AS 'Cust ID',
	supplier.buyer_id AS 'Supplier Buyer ID',
	supplier.date_last_modified AS 'Supplier Last Update',
	supplier.last_maintained_by AS 'Supplier Last Updated By',
	vendor_supplier.vendor_id AS 'Vendor ID',
	oe_hdr.order_date AS 'SO Date',
	oe_hdr.carrier_id AS 'Carrier ID',
	oe_line_po.po_no AS 'Newest PO #'
FROM
	P21.dbo.customer customer,
	P21.dbo.freight_code freight_code,
	P21.dbo.inv_mast inv_mast,
	P21.dbo.oe_hdr oe_hdr,
	P21.dbo.supplier supplier,
	P21.dbo.vendor_supplier vendor_supplier,
	P21.dbo.oe_line parent_oe_line
	LEFT JOIN
		P21.dbo.oe_line_po oe_line_po ON parent_oe_line.order_no = oe_line_po.order_number
	AND
		oe_line_po.line_number = parent_oe_line.line_no
WHERE 
	    parent_oe_line.oe_hdr_uid = oe_hdr.oe_hdr_uid
	AND customer.customer_id = oe_hdr.customer_id
	AND inv_mast.inv_mast_uid = parent_oe_line.inv_mast_uid
	AND freight_code.freight_code_uid = oe_hdr.freight_code_uid
	AND supplier.supplier_id = parent_oe_line.supplier_id
	AND vendor_supplier.supplier_id = supplier.supplier_id
	AND (oe_hdr.completed='N')
	AND (oe_hdr.projected_order='N')
	AND (parent_oe_line.complete='N')
	AND (inv_mast.other_charge_item='N')
	AND (oe_hdr.location_id=$10)
	AND (oe_hdr.validation_status<>'HOLD')
	AND (oe_hdr.rma_flag='N') AND (vendor_supplier.primary_vendor='Y')
	AND ((parent_oe_line.disposition='D') OR (parent_oe_line.disposition='S'))
	AND ((oe_line_po.po_no Is Null)
		OR ((oe_line_po.date_created = 
		       (SELECT DISTINCT
				MAX(oe_line_po.date_created) OVER(PARTITION BY oe_line.oe_line_uid ORDER BY oe_line.line_no)
			FROM
				P21.dbo.oe_line oe_line
				LEFT JOIN
					P21.dbo.oe_line_po oe_line_po ON oe_line.order_no = oe_line_po.order_number
				AND
					oe_line_po.line_number = oe_line.line_no
			WHERE
				    (oe_line.complete='N')
				AND ((oe_line.disposition='D') OR (oe_line.disposition='S'))
				AND (parent_oe_line.oe_line_uid = oe_line.oe_line_uid)
			))
		AND (oe_line_po.cancel_flag = 'Y')))

ORDER BY
	parent_oe_line.disposition, supplier.buyer_id, supplier.supplier_name, oe_hdr.order_no, parent_oe_line.line_no
