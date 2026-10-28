from collections import deque, namedtuple


# Значения по умолчанию
OPTIONS = {
    "root": 1,
    "height": 5,
    "left_leaf": lambda x: x * 2,
    "right_leaf": lambda x: x + 3,
}

# Позволяет отличать пропущенный аргумент от явно переданного значения.
UNSET = object()

Node = namedtuple("Node", ["value", "left", "right"])


def get_parameters(root, height, left_leaf, right_leaf):
    """Подставляет значения из OPTIONS и проверяет параметры."""
    if root is UNSET:
        root = OPTIONS["root"]
    if height is UNSET:
        height = OPTIONS["height"]
    if left_leaf is UNSET:
        left_leaf = OPTIONS["left_leaf"]
    if right_leaf is UNSET:
        right_leaf = OPTIONS["right_leaf"]

    if isinstance(height, bool) or not isinstance(height, int):
        raise TypeError("Высота должна быть целым числом.")

    if height < 0:
        raise ValueError("Высота не может быть отрицательной.")

    if not callable(left_leaf) or not callable(right_leaf):
        raise TypeError("Алгоритмы потомков должны быть функциями.")

    return root, height, left_leaf, right_leaf


# 1. Рекурсивное построение дерева в виде словаря
def build_tree(
    root=UNSET,
    height=UNSET,
    left_leaf=UNSET,
    right_leaf=UNSET,
):
    root, height, left_leaf, right_leaf = get_parameters(
        root, height, left_leaf, right_leaf
    )

    def make_node(value, remaining):
        if remaining == 0:
            return {"value": value, "left": None, "right": None}

        return {
            "value": value,
            "left": make_node(left_leaf(value), remaining - 1),
            "right": make_node(right_leaf(value), remaining - 1),
        }

    return make_node(root, height)


# 2. Построение по уровням с помощью deque
def build_tree_with_deque(
    root=UNSET,
    height=UNSET,
    left_leaf=UNSET,
    right_leaf=UNSET,
):
    root, height, left_leaf, right_leaf = get_parameters(
        root, height, left_leaf, right_leaf
    )

    tree = {"value": root, "left": None, "right": None}
    queue = deque([(tree, height)])

    while queue:
        node, remaining = queue.popleft()

        if remaining == 0:
            continue

        for side, algorithm in [
            ("left", left_leaf),
            ("right", right_leaf),
        ]:
            child = {
                "value": algorithm(node["value"]),
                "left": None,
                "right": None,
            }

            node[side] = child
            queue.append((child, remaining - 1))

    return tree


# 3. Дополнительное представление узлов через namedtuple
def build_tree_with_namedtuple(
    root=UNSET,
    height=UNSET,
    left_leaf=UNSET,
    right_leaf=UNSET,
):
    root, height, left_leaf, right_leaf = get_parameters(
        root, height, left_leaf, right_leaf
    )

    def make_node(value, remaining):
        if remaining == 0:
            return Node(value, None, None)

        return Node(
            value,
            make_node(left_leaf(value), remaining - 1),
            make_node(right_leaf(value), remaining - 1),
        )

    return make_node(root, height)


def to_dict(node):
    """Преобразует дерево из namedtuple во вложенные словари."""
    if node is None:
        return None

    return {
        "value": node.value,
        "left": to_dict(node.left),
        "right": to_dict(node.right),
    }


def print_tree(tree, level=0, label="Корень"):
    """Печатает дерево из словарей или namedtuple."""
    if tree is None:
        return

    if isinstance(tree, dict):
        value = tree["value"]
        left = tree["left"]
        right = tree["right"]
    else:
        value = tree.value
        left = tree.left
        right = tree.right

    print("    " * level + f"{label}: {value}")
    print_tree(left, level + 1, "Л")
    print_tree(right, level + 1, "П")


if __name__ == "__main__":
    root_text = input(
        f"Введите корень [по умолчанию {OPTIONS['root']}]: "
    ).strip()

    height_text = input(
        f"Введите высоту [по умолчанию {OPTIONS['height']}]: "
    ).strip()

    # Если пользователь ничего не ввёл, аргумент остаётся пропущенным.
    root = int(root_text) if root_text else UNSET
    height = int(height_text) if height_text else UNSET

    print("\n1. Рекурсивное построение")
    tree = build_tree(root=root, height=height)
    print_tree(tree)

    print("\n2. Построение с помощью deque")
    tree_deque = build_tree_with_deque(root=root, height=height)
    print("Результаты совпадают:", tree == tree_deque)

    print("\n3. Представление через namedtuple")
    tree_named = build_tree_with_namedtuple(root=root, height=height)
    print("Результаты совпадают:", tree == to_dict(tree_named))

    print("\n4. Собственные алгоритмы")
    custom_tree = build_tree(
        root=10,
        height=2,
        left_leaf=lambda x: x - 1,
        right_leaf=lambda x: x + 1,
    )
    print_tree(custom_tree)
