Task A
a1
#include <stdio.h>
#include <string.h>

#define MAX_DONORS 100

typedef struct {
    int day, month, year;
} Date;

typedef struct {
    int id;
    char name[30];
    char group[4];
    int age;
    char phone[11];
    Date lastDonation;
} Donor;

typedef struct {
    int count;
    int youngest_id;
} EligibilityResult;

// A1.2: register donor by pointer
void register_donor(Donor *d, int id, const char *name, const char *group, int age, const char *phone, Date d_date) {
    d->id = id;
    strncpy(d->name, name, sizeof(d->name) - 1);
    d->name[sizeof(d->name) - 1] = '\0';
    strncpy(d->group, group, sizeof(d->group) - 1);
    d->group[sizeof(d->group) - 1] = '\0';
    d->age = age;
    strncpy(d->phone, phone, sizeof(d->phone) - 1);
    d->phone[sizeof(d->phone) - 1] = '\0';
    d->lastDonation = d_date;
}

// A1.2: Check eligibility (const pointer for search key to guarantee read-only access)
int is_eligible(const Donor *d, const char *target_group) {
    return (strcmp(d->group, target_group) == 0 && d->age >= 18 && d->age <= 65);
}

// A1.3 Alternative 1: Returning multiple values via pointers
void get_eligible_ptrs(const Donor donors[], int n, const char *target_group, int *count_out, int *youngest_id_out) {
    *count_out = 0;
    *youngest_id_out = -1;
    int min_age = 1000;

    for (int i = 0; i < n; i++) {
        if (is_eligible(&donors[i], target_group)) {
            (*count_out)++;
            if (donors[i].age < min_age) {
                min_age = donors[i].age;
                *youngest_id_out = donors[i].id;
            }
        }
    }
}

// A1.3 Alternative 2: Returning multiple values via a Structure
EligibilityResult get_eligible_struct(const Donor donors[], int n, const char *target_group) {
    EligibilityResult res = {0, -1};
    int min_age = 1000;

    for (int i = 0; i < n; i++) {
        if (is_eligible(&donors[i], target_group)) {
            res.count++;
            if (donors[i].age < min_age) {
                min_age = donors[i].age;
                res.youngest_id = donors[i].id;
            }
        }
    }
    return res;
}

int main(void) {
    Donor list[3];
    register_donor(&list[0], 101, "Subhajit", "O+", 21, "9876543210", (Date){10, 5, 2026});
    register_donor(&list[1], 102, "Amit", "O+", 19, "9876543211", (Date){12, 6, 2026});
    register_donor(&list[2], 103, "Rohan", "A+", 24, "9876543212", (Date){1, 7, 2026});

    int count = 0, y_id = -1;
    get_eligible_ptrs(list, 3, "O+", &count, &y_id);
    printf("Pointer Approach -> Eligible: %d, Youngest ID: %d\n", count, y_id);

    EligibilityResult res = get_eligible_struct(list, 3, "O+");
    printf("Struct Approach  -> Eligible: %d, Youngest ID: %d\n", res.count, res.youngest_id);

    return 0;
}
a2
#include <stdio.h>

// A2.1: Recursive digital root (sums digits repeatedly until single digit remains)
int digital_root_recursive(int n) {
    if (n < 10) return n; // Base case
    
    int sum = 0;
    while (n > 0) {
        sum += n % 10;
        n /= 10;
    }
    return digital_root_recursive(sum); // Recursive call
}

// Alternative Concept: O(1) Mathematical formula
int digital_root_formula(int n) {
    if (n == 0) return 0;
    return 1 + (n - 1) % 9;
}

// A2.1: Halving power recursion: base^n = (base^(n/2))^2
long long power_halving(long long base, int exp) {
    if (exp == 0) return 1; // Base case
    
    long long half = power_halving(base, exp / 2);
    if (exp % 2 == 0) {
        return half * half;
    } else {
        return half * half * base;
    }
}

int main(void) {
    int donor_id = 7894;
    printf("Check Digit for ID %d (Recursive): %d\n", donor_id, digital_root_recursive(donor_id));
    printf("Check Digit for ID %d (O(1) Formula): %d\n", donor_id, digital_root_formula(donor_id));

    printf("2^10 using Halving Recursion: %lld\n", power_halving(2, 10));
    return 0;
}
a3
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// 8 Blood Groups: 0:O+, 1:O-, 2:A+, 3:A-, 4:B+, 5:B-, 6:AB+, 7:AB-
const char *group_names[8] = {"O+", "O-", "A+", "A-", "B+", "B-", "AB+", "AB-"};

// A3.2: Finds the blood group with lowest stock and returns a pointer to it
// Note: Returns a pointer to the caller's array element, avoiding dangling pointers
int* find_lowest_stock(int stock[], int n) {
    int min_idx = 0;
    for (int i = 1; i < n; i++) {
        if (stock[i] < stock[min_idx]) {
            min_idx = i;
        }
    }
    return &stock[min_idx]; // Safe return: memory belongs to stock[] in caller
}

int main(int argc, char *argv[]) {
    int stock[8] = {40, 15, 25, 8, 30, 12, 10, 5};
    int *p = stock;

    // A3.1 Pointer operations demonstration
    printf("--- Pointer Operations Demo ---\n");
    printf("Base stock address (p)        = %p\n", (void*)p);
    printf("Address of 4th element (p + 3) = %p\n", (void*)(p + 3));
    printf("Value at *(p + 3)              = %d\n", *(p + 3));
    printf("Value at p[3]                  = %d\n", p[3]);
    printf("Dereference and post-increment = %d\n", *p++);
    printf("Value pointed to after ++      = %d\n", *p);

    // Reset pointer
    p = stock;

    // A3.2: Update lowest stock directly using the returned pointer
    int *lowest = find_lowest_stock(stock, 8);
    printf("\nLowest stock before replenishment: %d\n", *lowest);
    *lowest += 10; // Directly updates the lowest stock element
    printf("Lowest stock after adding 10 units: %d\n", *lowest);

    // A3.3: Command-Line Argument Interface
    printf("\n--- Command-Line Interface Parsing ---\n");
    if (argc < 2) {
        printf("Usage: %s <command> [args]\n", argv[0]);
        printf("Commands: find <group> | issue <group> <units>\n");
        return 0;
    }

    if (strcmp(argv[1], "find") == 0) {
        if (argc < 3) {
            printf("Error: Missing blood group argument.\n");
            return 1;
        }
        printf("Executing command: Finding donors for %s\n", argv[2]);
    } else if (strcmp(argv[1], "issue") == 0) {
        if (argc < 4) {
            printf("Error: Missing units or blood group.\n");
            return 1;
        }
        printf("Executing command: Issuing %s units of %s\n", argv[3], argv[2]);
    } else {
        printf("Error: Invalid command '%s'\n", argv[1]);
    }

    return 0;
}
TaskB
B1
#include <stdio.h>

int calls = 0;

// Linear recursive power function
long long power_linear(long long base, long long exp) {
    calls++;
    if (exp == 0) return 1;
    return base * power_linear(base, exp - 1);
}

// Fast recursive power function using base^(n/2)
long long power_fast(long long base, long long exp) {
    calls++;
    
    // TODO 1: Write the base case for exp == 0; otherwise compute half =
    // power_fast(base, exp / 2) ONCE and return half * half, multiplied by base when
    // exp is odd
    if (exp == 0) {
        return 1;
    }
    
    long long half = power_fast(base, exp / 2);
    
    if (exp % 2 == 0) {
        return half * half;
    } else {
        return half * half * base;
    }
}

// Digital Root implementation for completeness (Task A2)
int digital_root(int n) {
    if (n < 10) return n;
    int sum = 0;
    while (n > 0) {
        sum += n % 10;
        n /= 10;
    }
    return digital_root(sum);
}

int main(void) {
    calls = 0;
    long long a = power_linear(2, 30);
    printf("linear: %lld in %d calls\n", a, calls);
    
    calls = 0;
    long long b = power_fast(2, 30);
    printf("fast : %lld in %d calls\n", b, calls);
    
    return 0;
}
b3
#include <stdio.h>

typedef struct { 
    int group_code; 
    int units; 
} Stock; /* fixed-size binary record */

/* Overwrites the units of record number index directly in the binary file. */
int update_units(FILE *fp, int index, int units) {
    Stock s;
    
    // TODO 1: Seek to record index, read it, change units, seek back by one record and
    // write it again; return 0 if any step fails, 1 on success
    
    // 1. Seek to the specified record index
    if (fseek(fp, index * sizeof(Stock), SEEK_SET) != 0) return 0;
    
    // 2. Read the record
    if (fread(&s, sizeof(Stock), 1, fp) != 1) return 0;
    
    // 3. Update the units
    s.units = units;
    
    // 4. Seek backwards by exactly one record size to overwrite it
    if (fseek(fp, -sizeof(Stock), SEEK_CUR) != 0) return 0;
    
    // 5. Write the updated record
    if (fwrite(&s, sizeof(Stock), 1, fp) != 1) return 0;
    
    return 1;
}

int main(void) {
    FILE *fp = tmpfile();
    if (fp == NULL) { 
        perror("tmpfile"); 
        return 1; 
    }
    
    Stock initial[5] = {{1, 40}, {2, 25}, {3, 12}, {4, 8}, {5, 30}};
    fwrite(initial, sizeof(Stock), 5, fp);
    
    printf("update record 3: %s\n", update_units(fp, 3, 20) ? "done" : "failed");
    printf("update record 9: %s\n", update_units(fp, 9, 20) ? "done" : "failed");
    
    rewind(fp);
    Stock s;
    while (fread(&s, sizeof(s), 1, fp) == 1) {
        printf("%d:%d ", s.group_code, s.units);
    }
    printf("\n");
    
    fclose(fp);
    return 0;
}#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct {
    int id;
    char group[4];
} Donor;

typedef struct {
    Donor *data;
    int size;
    int capacity;
} DonorList;

int add_donor(DonorList *list, int id, const char *group) {
    if (list->size == list->capacity) {
        // TODO 1: Compute the new capacity (2 if it is 0, otherwise double it), grow
        // the array with realloc WITHOUT losing the old pointer if realloc fails, and
        // update data and capacity
        
        int new_capacity = (list->capacity == 0) ? 2 : list->capacity * 2;
        
        // Use a temporary pointer to protect the original data if realloc fails
        Donor *temp = realloc(list->data, new_capacity * sizeof(Donor));
        
        if (temp == NULL) {
            return 0; // Failure, original list->data remains intact
        }
        
        list->data = temp;
        list->capacity = new_capacity;
    }
    
    list->data[list->size].id = id;
    strncpy(list->data[list->size].group, group, 3);
    list->data[list->size].group[3] = '\0';
    list->size++;
    
    return 1;
}

int main(void) {
    DonorList list = {NULL, 0, 0};
    const char *groups[] = {"O+", "A+", "B-", "AB+", "O-"};
    
    for (int i = 0; i < 5; i++) {
        add_donor(&list, 7001 + i, groups[i]);
        printf("added %d (%s): size = %d, capacity = %d\n",
               list.data[i].id, list.data[i].group, list.size, list.capacity);
    }
    
    // Prevent memory leaks
    free(list.data);
    
    return 0;
}
b4
