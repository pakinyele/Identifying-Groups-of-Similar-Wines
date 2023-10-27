# Identifying-Groups-of-Similar-Wines


'''1. LOAD CSV FUNCTION: In this function,The csv file was loaded from the moodle page and return the CSV file as a matrix, such that the for each line in the csv file, it creates a list and separates
 at the comma.''' 
def load_from_csv(a):
    fin=open(a)
    b=[]
    for line in fin:
        l = line.split(",")
        new_l = []
        for element in l:
            new_l.append(float(element))
        b.append(new_l)
    fin.close()
    return b


'''2. GET DISTANCE FUNCTION: In this function, as seen in the appendix, the Manhattan Distance between two lists(the parameters 'a' and 'b' created) was returned.'''
def get_distance(a,b):
    total = 0
    for x, y in zip(a, b):
        res = abs(x-y)
        total += res
    return total


'''COLUMN FUNCTION: This column function was created to append element in each row of the matrix, thereby creating a new list 'lst'.
This will be used in answering subsequent questions (such as GET_MIN and GET_MAX).
Therefore for each row in the matrix and element in each row, if index of the row in each element is same as 'a',
the empty list 'lst' then appends to the element in the row.
Therefore, the column function with was created.'''
def column(matrix,a):
    lst = []
    for row in matrix:
        lst.append(row[a])
    return lst


'''3. GET MAX FUNCTION: In this function,two parameters were created; a 'matrix'(which is a list of lists) and column number 'a'.
The function looks for the highest value in the elements of the data matrix in the column number.'''
def get_max(matrix,a):
    lst1= column(matrix,a)
    return max(lst1)


'''4. GET MIN FUNCTION: In this function,two parameters; a 'matrix'(which is a list of lists) and column number 'a' were created.
The function looks for the lowest value in the elements of the data matrix in the column number.'''
def get_min(matrix, a):
    lst2= column(matrix,a)
    return min(lst2)


'''#5. GET STANDARDISED MATRIX : In this function,a parameter with 'matrix' (which is a list of lists).
#The function then returns a 'new_matrix' list which is a standardised version of the matrix passed as a parameter.'''

def get_standardised_matrix(matrix):
    new_matrix = []
    for row in matrix:
        lst3=[]
        for element in row:
            i=row.index(element)
            n_col= column(matrix, i)
            avg= sum(n_col)/len(n_col)
            maxi= get_max(matrix, i)
            mini= get_min(matrix, i)
            stand_data= (element - avg)/(maxi-mini)
            lst3.append(stand_data)
        new_matrix.append(lst3)
    return new_matrix


'''6. GET MEDIAN FUNCTION: In this functions two parameters were created. The matrix(which is a list of lists) and column number 'a'.
#This function then returns the median of the data matrix at the column number passed as a parameter(n_col).
#The get_median function is passed such that it sorts the column of the matrix and a parameters.
#If the function is either even or odd, then the function return the median.'''
def get_median(matrix, a):
    n_col = column(matrix, a)
    n_col.sort()
    if len(n_col)%2==1:
        return n_col[len(n_col)//2]
    try:
        a_i, b_i = len(n_col)//2, len(n_col)//2+1
        a, b = n_col[a_i], n_col[b_i]
        return (a+b)/2
    except:
        pass


'''CLUSTERING ALGORITHM

#7. GET GROUPS FUNCTION: In this function, two parameters were created, the 'matrix'(which is a list of lists) and the number of groups to be defined 'K'.
#It then returns a list S such that the row to the cluster nearest to C is assigned to it, creating a new list (S.append(min_dist)).
#When there is no observable change in S, the process then stops.''' 
def get_groups(matrix, K):
    from random import randint
    c= len(matrix)
    l=[]
    for i in range(K):
        rand= randint(0,c-1)
        while rand in l:
            rand= randint(0,c-1)
        l.append(rand)
    c_mat_new=[]
    for index in l:
        c_mat_new.append(matrix[index])
    S=[]
    for row in matrix:
        dist_dict = {}
        for i, centroid in enumerate(c_mat_new):
            dist_dict[i] = get_distance(row, centroid)
        min_dist = min(dist_dict, key=dist_dict.get)
        S.append(min_dist)
    c_mat = None
    while not c_mat == c_mat_new:
        c_mat = c_mat_new
        c_mat_new = get_centroids(matrix, S, K)
        S=[]
        for row in matrix:
            dist_dict = {}
            for i, centroid in enumerate(c_mat_new):
                dist_dict[i] = get_distance(row, centroid)
            min_dist = min(dist_dict, key=dist_dict.get)
            S.append(min_dist)
    return S
    return (a+b)/2


'''8. GET CENTROIDS FUNCTION: In this function, three parameters were created; the 'matrix' (which is a list of lists), the list 'S' and the value of 'K'.
 It then returns a list 'c_mat' containing the K elements such that each element of the c_mat should be equal to the median of the column, but only considering
the rows that have been assigned to cluster K. '''
def get_centroids(matrix, S, K):
    c_mat=[]
    for k in range(K):
        l=[]
        for i, row in enumerate(S):
            if row == k:
                l.append(matrix[i])
        l_median=[]
        for col in range(len(l[0])):
            l_median.append(get_median(l, col))
        c_mat.append(l_median)
    return c_mat


'''RUN_TEST FUNCTION: In this function,a series of tests is to be run such that an algorithm (get_groups) is returned when hardcoded values of range(2,7), ie 2, 3, 4, 5, 6.
#is run to show how many wines have been assigned to each of the groups, by displaying 'The cluster .. has .. entit(ies) of wines assigned'. '''
def run_test():
    matrix = load_from_csv('Data.csv')
    mat = get_standardised_matrix(matrix)
    for k in range(2,7):
        g = get_groups(mat, k)
        for i, value in enumerate(set(g)):
            print(f"The cluster {i+1} has {g.count(value)} entities of wines assigned")
        print()
                
run_test()

