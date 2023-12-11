#include <stdio.h>
#include <stdlib.h>

typedef struct Node {
    int id;
    char status;  // 'v' for vacant, 'o' for occupied
    struct Node* next;
} Parking_Space_Node;

typedef struct {
    int total_Spaces;
    Parking_Space_Node* parking_Spaces;
} Parking_Lot;

void initialize(Parking_Lot *lot, int total_Spaces) {
    lot->total_Spaces = total_Spaces;
    lot->parking_Spaces = NULL;

    for (int i = total_Spaces; i > 0; --i) {
        Parking_Space_Node* new_node = (Parking_Space_Node*)malloc(sizeof(Parking_Space_Node));
        if (new_node == NULL) {
            printf("Memory allocation failed. Exiting...\n");
            exit(1);
        }

        new_node->id = i;
        new_node->status = 'v';
        new_node->next = lot->parking_Spaces;

        lot->parking_Spaces = new_node;
    }
}

void availability(Parking_Lot *lot) {
    printf("Available spaces: ");
    Parking_Space_Node* current = lot->parking_Spaces;
    while (current != NULL) {
        if (current->status == 'v') {
            printf("%d ", current->id);
        }
        current = current->next;
    }
    printf("\n");
}

void park_Car(Parking_Lot *lot, int space_Id) {
    Parking_Space_Node* current = lot->parking_Spaces;
    while (current != NULL && current->id != space_Id) {
        current = current->next;
    }

    if (current == NULL) {
        printf("Invalid space ID %d.\n", space_Id);
        return;
    }

    if (current->status == 'v') {
        current->status = 'o';
        printf("Car parked at space %d.\n", space_Id);
    } else {
        printf("Space %d is already occupied.\n", space_Id);
    }
}

void leave(Parking_Lot *lot, int space_Id) {
    Parking_Space_Node* current = lot->parking_Spaces;
    while (current != NULL && current->id != space_Id) {
        current = current->next;
    }

    if (current == NULL) {
        printf("Invalid space ID %d.\n", space_Id);
        return;
    }

    if (current->status == 'o') {
        current->status = 'v';
        printf("Car left space %d.\n", space_Id);
    } else {
        printf("Space %d is already vacant.\n", space_Id);
    }
}

void cleanup(Parking_Lot *lot) {
    Parking_Space_Node* current = lot->parking_Spaces;
    while (current != NULL) {
        Parking_Space_Node* next = current->next;
        free(current);
        current = next;
    }
}

int main() {
    int total_Lots;

    printf("Enter the number of parking lots: ");
    scanf("%d", &total_Lots);

    if (total_Lots <= 0 || total_Lots > 100) {
        printf("Invalid number of parking lots. Exiting...\n");
        return 1;
    }

    Parking_Lot parking_Lot;
    initialize(&parking_Lot, total_Lots);

    availability(&parking_Lot);

    char action;
    int space_Id;

    while (1) {
        printf("Do you want to park a car (P) or leave a parking space (L)? Enter Q to quit: ");
        scanf(" %c", &action);

        if (action == 'P' || action == 'p') {
            printf("Enter the space number to park the car: ");
            scanf("%d", &space_Id);
            park_Car(&parking_Lot, space_Id);
        } else if (action == 'L' || action == 'l') {
            printf("Enter the space number to leave: ");
            scanf("%d", &space_Id);
            leave(&parking_Lot, space_Id);
        } else if (action == 'Q' || action == 'q') {
            break;
        } else {
            printf("Invalid option. Please enter P to park, L to leave, or Q to quit.\n");
        }

        availability(&parking_Lot);
    }

    cleanup(&parking_Lot);

    return 0;
}
