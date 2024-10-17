#include <stdio.h>
#include <stdlib.h>

struct Node {
    int coef;
    int exp;
    struct Node* next;
};

typedef struct Node Node;

void insert(Node** poly, int coef, int exp) {
    Node* temp = (Node*) malloc(sizeof(Node));
    temp->coef = coef;
    temp->exp = exp;
    temp->next = NULL;

    if (*poly == NULL) {
        *poly = temp;
        return;
    }

    Node* current = *poly;
    
    // Insert while maintaining the order based on the exponent
    while (current->next != NULL && current->next->exp > exp) {
        current = current->next;
    }
    
    if (current->exp == exp) {
        // If the exponent already exists, add the coefficients
        current->coef += coef;
        free(temp);  // No need to keep the new node
    } else {
        // Insert the new node in the right place
        temp->next = current->next;
        current->next = temp;
    }
}

void print(Node* poly) {
    if (poly == NULL) {
        printf("0\n");
        return;
    }
    
    Node* current = poly;
    
    while (current != NULL) {
        printf("%dx^%d", current->coef, current->exp);
        current = current->next;
        if (current != NULL) {
            printf(" + ");
        }
    }
    
    printf("\n");
}

Node* add(Node* poly1, Node* poly2) {
    Node* result = NULL;
    
    while (poly1 != NULL && poly2 != NULL) {
        if (poly1->exp == poly2->exp) {
            insert(&result, poly1->coef + poly2->coef, poly1->exp);
            poly1 = poly1->next;
            poly2 = poly2->next;
        } else if (poly1->exp > poly2->exp) {
            insert(&result, poly1->coef, poly1->exp);
            poly1 = poly1->next;
        } else {
            insert(&result, poly2->coef, poly2->exp);
            poly2 = poly2->next;
        }
    }
    
    while (poly1 != NULL) {
        insert(&result, poly1->coef, poly1->exp);
        poly1 = poly1->next;
    }
    
    while (poly2 != NULL) {
        insert(&result, poly2->coef, poly2->exp);
        poly2 = poly2->next;
    }
    
    return result;
}

void free_poly(Node* poly) {
    Node* current = poly;
    while (current != NULL) {
        Node* next = current->next;
        free(current);
        current = next;
    }
}

int main() {
    Node* poly1 = NULL;
    Node* poly2 = NULL;
    int n, coef, exp;

    // Input for the first polynomial
    printf("Enter the number of terms for the first polynomial: ");
    scanf("%d", &n);
    for (int i = 0; i < n; i++) {
        printf("Enter coefficient and exponent for term %d: ", i + 1);
        scanf("%d %d", &coef, &exp);
        insert(&poly1, coef, exp);
    }

    // Input for the second polynomial
    printf("Enter the number of terms for the second polynomial: ");
    scanf("%d", &n);
    for (int i = 0; i < n; i++) {
        printf("Enter coefficient first and then exponent for term %d: ", i + 1);
        scanf("%d %d", &coef, &exp);
        insert(&poly2, coef, exp);
    }

    // Print the polynomials
    printf("First polynomial: ");
    print(poly1);
    printf("Second polynomial: ");
    print(poly2);

    // Add the polynomials
    Node* result = add(poly1, poly2);
    printf("Result: ");
    print(result);

    // Free allocated memory
    free_poly(poly1);
    free_poly(poly2);
    free_poly(result);

    return 0;
}
