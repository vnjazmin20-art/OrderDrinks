/* Order Drinks | original script.js lines 3699-3818 */
/* =========================================================
   DRINK STATUS
========================================================= */
function syncDrinkStatus(
    drink
) {
    drink.status =
        drink.stock > 0
            ? "Available"
            : "Out of Stock";
}

/* =========================================================
   POPULATE DRINK CATEGORY SELECTS
========================================================= */
function populateCategorySelects() {
    const ids = [
        "newDrinkCategory",
        "editDrinkCategory"
    ];
    ids.forEach(
        id => {
            const select =
                document.getElementById(
                    id
                );
            if (!select) {
                return;
            }
            const previous =
                select.value;
            select.innerHTML = "";
            DRINK_CATEGORIES.forEach(
                category => {
                    const option =
                        document.createElement(
                            "option"
                        );
                    option.value =
                        category;
                    option.textContent =
                        category;
                    select.appendChild(
                        option
                    );
                }
            );
            if (
                [...select.options]
                    .some(
                        option =>
                            option.value ===
                            previous
                    )
            ) {
                select.value =
                    previous;
            }
        }
    );
}

function populateSnackSelect(selectId, category, selected = []) {
    const select = document.getElementById(selectId);
    if (!select) return;
    select.innerHTML = "";
    const placeholder = document.createElement("option");
    placeholder.value = "";
    placeholder.textContent = "Select free snack";
    select.appendChild(placeholder);
    const choices = DEFAULT_SNACKS_BY_CATEGORY[category] || [];
    const selectedValue = Array.isArray(selected) && selected.length ? selected[0] : "";
    choices.forEach(name => {
        const option = document.createElement("option");
        option.value = name;
        option.textContent = name;
        option.selected = name === selectedValue;
        select.appendChild(option);
    });
}

function toggleSnackConfig(mode) {
    const categoryEl = document.getElementById(mode === "new" ? "newDrinkCategory" : "editDrinkCategory");
    const group = document.getElementById(mode === "new" ? "newSnackGroup" : "editSnackGroup");
    const selectId = mode === "new" ? "newDrinkSnacks" : "editDrinkSnacks";
    if (!categoryEl || !group) return;
    const show = categoryHasFreeSnack(categoryEl.value);
    group.style.display = show ? "block" : "none";
    if (show) {
        let selected = [];
        if (mode === "edit") {
            const drink = manualFind(drinks, d => d.id === document.getElementById("editDrinkSelect")?.value);
            selected = getDrinkSnackChoices(drink);
        } else selected = [];
        populateSnackSelect(selectId, categoryEl.value, selected);
    }
}

function selectedSnackValues(selectId) {
    const el = document.getElementById(selectId);
    if (!el || !el.value) return [];
    return [el.value];
}

function updateOrderSnackChoices() {
    const drink = manualFind(drinks, d => d.id === document.getElementById("drinkSelect")?.value);
    const group = document.getElementById("orderSnackGroup");
    const select = document.getElementById("orderSnackSelect");
    if (!group || !select) return;
    const choices = getDrinkSnackChoices(drink);
    group.style.display = choices.length ? "block" : "none";
    select.innerHTML = "";
    choices.forEach(name => {
        const option = document.createElement("option");
        option.value = name;
        option.textContent = `Free Snack: ${name}`;
        select.appendChild(option);
    });
}


/* Order Drinks | original script.js lines 4136-4504 */
/* =========================================================
   POPULATE DRINK SELECTS
========================================================= */
function populateDrinkSelect() {
    const ids = [
        "drinkSelect",
        "editDrinkSelect"
    ];
    ids.forEach(
        id => {
            const select =
                document.getElementById(
                    id
                );
            if (!select) {
                return;
            }
            const previous =
                select.value;
            select.innerHTML =
                "";
            const list =
                id === "drinkSelect"
                ?
                manualFilter(drinks, 
                    drink =>
                        drink.status ===
                            "Available"
                        &&
                        drink.stock > 0
                )
                :
                drinks;
            list.forEach(
                drink => {
                    const option =
                        document.createElement(
                            "option"
                        );
                    option.value =
                        drink.id;
                    if (
                        id ===
                        "drinkSelect"
                    ) {
                        option.textContent =
                            `${drink.name} \u2014 \u20B1${drink.price} \u2014 Stock: ${drink.stock}`;
                    }
                    else {
                        option.textContent =
                            `${drink.name} \u2014 Stock: ${drink.stock}`;
                    }
                    select.appendChild(
                        option
                    );
                }
            );
            if (
                [...select.options]
                    .some(
                        option =>
                            option.value ===
                            previous
                    )
            ) {
                select.value =
                    previous;
            }
        }
    );
    loadDrinkForEdit();
    updateOrderSnackChoices();
}

/* =========================================================
   POPULATE DRINK ORDER TARGET
========================================================= */
function populateDrinkOrderTargetSelect() {
    const select =
        document.getElementById(
            "drinkOrderTarget"
        );
    if (!select) {
        return;
    }
    const previous =
        select.value;
    select.innerHTML = "";
    const newOrderOption =
        document.createElement(
            "option"
        );
    newOrderOption.value =
        "new_drinkonly";
    newOrderOption.textContent =
        "New Drink-Only Order";
    select.appendChild(
        newOrderOption
    );
    Object.values(
        sessions
    ).forEach(
        session => {
            const option =
                document.createElement(
                    "option"
                );
            option.value =
                "session:" +
                session.facilityId;
            option.textContent =
                `${session.facilityName} \u2014 ${session.customerName}`;
            select.appendChild(
                option
            );
        }
    );
    drinkOnlyOrders.forEach(
        order => {
            const option =
                document.createElement(
                    "option"
                );
            option.value =
                "do:" +
                order.id;
            option.textContent =
                `Drink-Only ${order.id} (${order.drinks.length} item lines)`;
            select.appendChild(
                option
            );
        }
    );
    if (
        [...select.options]
            .some(
                option =>
                    option.value ===
                    previous
            )
    ) {
        select.value =
            previous;
    }
}

/* =========================================================
   PLACE DRINK ORDER
========================================================= */
async function handleDrinkOrder() {
    const targetInput =
        document.getElementById(
            "drinkOrderTarget"
        );
    const drinkInput =
        document.getElementById(
            "drinkSelect"
        );
    const quantityInput =
        document.getElementById(
            "drinkQty"
        );
    if (
        !targetInput ||
        !drinkInput ||
        !quantityInput
    ) {
        return;
    }
    const target =
        targetInput.value;
    const drinkId =
        drinkInput.value;
    const quantity =
        parseInt(
            quantityInput.value
        );
    const drink =
        manualFind(drinks, 
            item =>
                item.id === drinkId
        );
    if (
        !drink ||
        isNaN(quantity) ||
        quantity <= 0
    ) {
        showMsg(
            "drinkOrderMsg",
            "Select a drink and enter a valid quantity.",
            "warn"
        );
        return;
    }
    if (
        drink.status ===
            "Out of Stock"
        ||
        drink.stock <
            quantity
    ) {
        showMsg(
            "drinkOrderMsg",
            `Not enough stock for ${drink.name}. Available stock: ${drink.stock}.`,
            "error"
        );
        return;
    }
    const snackName = categoryHasFreeSnack(drink.category)
        ? document.getElementById("orderSnackSelect")?.value
        : "";
    if (categoryHasFreeSnack(drink.category) && !snackName) {
        showMsg("drinkOrderMsg", "Select the free snack before placing this order.", "warn");
        return;
    }
    const confirmed =
        await showActionConfirmation(
            "Confirm Order",
            `Place ${quantity}x ${drink.name} for the selected order?`,
            "Place Order"
        );
    if (!confirmed) {
        return;
    }
    drink.stock -=
        quantity;
    syncDrinkStatus(
        drink
    );
    const line = {
        drinkId:
            drink.id,
        name:
            drink.name,
        qty:
            quantity,
        price:
            drink.price,
        freeSnack: snackName || null
    };
    /* ---------- NEW DRINK-ONLY ORDER ---------- */
    if (
        target ===
        "new_drinkonly"
    ) {
        const order = {
            id:
                `DO${String(nextDrinkOnlyId)
                    .padStart(3, "0")}`,
            drinks: [
                line
            ],
            createdAt:
                new Date()
        };
        nextDrinkOnlyId++;
        manualAppend(drinkOnlyOrders,
            order
        );
        showMsg(
            "drinkOrderMsg",
            `${quantity}x ${drink.name} was added to new drink-only order ${order.id}.`,
            "success"
        );
    }
    /* ---------- EXISTING DRINK-ONLY ORDER ---------- */
    else if (
        target.startsWith(
            "do:"
        )
    ) {
        const orderId =
            target.slice(3);
        const order =
            manualFind(drinkOnlyOrders, 
                item =>
                    item.id ===
                    orderId
            );
        if (!order) {
            drink.stock +=
                quantity;
            syncDrinkStatus(
                drink
            );
            showMsg(
                "drinkOrderMsg",
                "Drink-only order was not found.",
                "error"
            );
            return;
        }
        manualAppend(order.drinks,
            line
        );
        showMsg(
            "drinkOrderMsg",
            `${quantity}x ${drink.name} was added to ${order.id}.`,
            "success"
        );
    }
    /* ---------- ACTIVE SESSION ---------- */
    else if (
        target.startsWith(
            "session:"
        )
    ) {
        const facilityId =
            target.slice(8);
        const session =
            sessions[
                facilityId
            ];
        if (!session) {
            drink.stock +=
                quantity;
            syncDrinkStatus(
                drink
            );
            showMsg(
                "drinkOrderMsg",
                "Selected session no longer exists.",
                "error"
            );
            return;
        }
        manualAppend(session.drinks,
            line
        );
        showMsg(
            "drinkOrderMsg",
            `${quantity}x ${drink.name} was added to ${session.customerName}'s bill.`,
            "success"
        );
    }
    else {
        drink.stock +=
            quantity;
        syncDrinkStatus(
            drink
        );
        showMsg(
            "drinkOrderMsg",
            "Invalid order target.",
            "error"
        );
        return;
    }
    if (snackName) {
        manualAppend(expenses, {
            id: `EXP${String(nextExpenseId++).padStart(3, "0")}`,
            createdAt: new Date(),
            source: drink.name,
            snack: snackName,
            qty: quantity,
            unitCost: Number(SNACK_CATALOG[snackName] || 0),
            total: Number(SNACK_CATALOG[snackName] || 0) * quantity,
            status: "Unpaid",
            paidAt: null
        });
        renderExpenses();
    }
    quantityInput.value =
        "1";
    renderInventory();
    populateAllSelects();
    renderBillPreview();
}


/* Order Drinks | original script.js lines 4677-4741 */
/* =========================================================
   DRINK TOTAL HELPERS
========================================================= */
function calculateDrinkTotal(
    drinkLines
) {
    if (
        !Array.isArray(
            drinkLines
        )
    ) {
        return 0;
    }
    return manualReduce(drinkLines, 
        (total, line) =>
            total +
            (
                Number(
                    line.price
                ) *
                Number(
                    line.qty
                )
            ),
        0
    );
}

/* =========================================================
   COMBINE SAME DRINK LINES
   Used for billing / receipt display.
========================================================= */
function combineDrinkLines(
    drinkLines
) {
    const combined = {};
    drinkLines.forEach(
        line => {
            const key =
                `${line.drinkId}-${line.price}`;
            if (
                !combined[key]
            ) {
                combined[key] = {
                    drinkId:
                        line.drinkId,
                    name:
                        line.name,
                    qty:
                        0,
                    price:
                        line.price
                };
            }
            combined[key].qty +=
                Number(
                    line.qty
                );
        }
    );
    return Object.values(
        combined
    );
}
