#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct BrowserTab {
    int pageID;
    char url[100];
    struct BrowserTab *next;
    struct BrowserTab *prev;
};

struct BrowserTab *current = NULL;
int idCounter = 1;

struct BrowserTab* makeTab(const char address[]) {
    struct BrowserTab *temp = (struct BrowserTab*)malloc(sizeof(struct BrowserTab));
    if (!temp) {
        printf("Memory allocation failed!\n");
        exit(1);
    }
    temp->pageID = idCounter++;
    strcpy(temp->url, address);
    temp->next = temp; 
    temp->prev = temp;
    return temp;
}

void openNewPage() {
    char address[100];
    printf("Enter URL: ");
    scanf("%99s", address);

    struct BrowserTab *newTab = makeTab(address);

    if (current == NULL) {
        current = newTab;
    } else {
        struct BrowserTab *nextNode = current->next;
        current->next = newTab;
        newTab->prev = current;
        newTab->next = nextNode;
        nextNode->prev = newTab;
        current = newTab;
    }
    printf("Opened: [%d] %s\n", current->pageID, current->url);
}

void nextTab() {
    if (current != NULL) {
        current = current->next;
        printf("Now at: [%d] %s\n", current->pageID, current->url);
    } else {
        printf("No tabs open!\n");
    }
}

void prevTab() {
    if (current != NULL) {
        current = current->prev;
        printf("Now at: [%d] %s\n", current->pageID, current->url);
    } else {
        printf("No tabs open!\n");
    }
}

void showTab() {
    if (current != NULL) {
        printf("Current tab: [%d] %s\n", current->pageID, current->url);
    } else {
        printf("No tabs open!\n");
    }
}

void closeTab() {
    if (current == NULL) {
        printf("No tab to close!\n");
        return;
    }

    printf("Closing: [%d] %s\n", current->pageID, current->url);

    if (current->next == current) {
        free(current);
        current = NULL;
        printf("All tabs closed!\n");
        return;
    }

    struct BrowserTab *prevNode = current->prev;
    struct BrowserTab *nextNode = current->next;

    prevNode->next = nextNode;
    nextNode->prev = prevNode;

    struct BrowserTab *temp = current;
    current = nextNode;
    free(temp);

    printf("Now at: [%d] %s\n", current->pageID, current->url);
}

void showHistory() {
    if (current == NULL) {
        printf("No tabs in history!\n");
        return;
    }

    printf("History:\n");
    struct BrowserTab *start = current;
    struct BrowserTab *temp = current;

    do {
        printf("[%d] %s\n", temp->pageID, temp->url);
        temp = temp->next;
    } while (temp != start);
}

int main() {
    int choice;

    do {
        printf("\nBrowser Menu\n");
        printf("1. Open New Page\n");
        printf("2. Next Tab\n");
        printf("3. Previous Tab\n");
        printf("4. Show Current Tab\n");
        printf("5. Close Current Tab\n");
        printf("6. Show History\n");
        printf("0. Exit\n");
        printf("Enter choice: ");
        scanf("%d", &choice);

        switch(choice) {
            case 1: openNewPage(); break;
            case 2: nextTab(); break;
            case 3: prevTab(); break;
            case 4: showTab(); break;
            case 5: closeTab(); break;
            case 6: showHistory(); break;
            case 0: printf("Exiting...\n"); break;
            default: printf("Invalid choice!\n");
        }
    } while (choice != 0);

    return 0;
}
