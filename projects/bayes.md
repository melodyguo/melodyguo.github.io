# Bayes Classifier
I wrote a Naive Bytes classifier in C++ which uses Bayes theorem and Laplace smoothing to determine the most likely object given a set of adjectives.

![bayes theorem](bayes.jpeg)

I used the extended form of Bayes theorem shown above

This helper function counts the occurences of object AND adjective (i.e. apple AND mushy) 
```
float occadj(string adj, string object, map<string, vector<set<string>>> naive, bool NOT) {
    float occ = 0;
    // occ of (adj AND ~object)
    if (NOT) {
        for (map<string, vector<set<string>>>::iterator it = naive.begin(); it != naive.end(); it++) {
            if (it->first != object) {
                vector<set<string>> objs = it->second;
                for (unsigned int i = 0; i < objs.size(); i++) {
                    if (objs[i].count(adj) > 0) {
                        occ++;
                    }
                }
            }
        }
        return occ;
    }
    // occ of (adj AND fruit)
    vector<set<string>> objs = naive.at(object);
    for (unsigned int i = 0; i < objs.size(); i++) {
        if (objs[i].count(adj) > 0) {
            occ++;
        }
    }
    return occ;
}
```
