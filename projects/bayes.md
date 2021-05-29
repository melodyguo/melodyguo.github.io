# Bayes Classifier
I wrote a Naive Bytes classifier in C++ which uses Bayes theorem and Laplace smoothing to determine the most likely object given a set of adjectives.


![bayes theorem](/images/bayes.jpg)

*Extended form of Bayes theorem.*

This helper function counts the occurences of object AND adjective (i.e. apple AND mushy). I wrote it to simplify the probability calculations that are performed later.
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

This helper function does the actually Bayes probability calculation given the set of objects and their respective adjectives.
```
float calcprob(string object, set<string> adj, int totalobjs, map<string, vector<set<string>>> naive) {
    float numobjs = naive.at(object).size();
    float po = numobjs / (float)totalobjs;  // P(object)

    float pagiveno = 1;     // P(adj | object)
    float pagivennoto = 1;  // P(adj | ~object)
    for (set<string>::iterator it = adj.begin(); it != adj.end(); it++) {
        float occ = occadj(*it, object, naive, false);
        pagiveno *= (1 + occ) / (1 + numobjs);
        float occnot = occadj(*it, object, naive, true);
        pagivennoto *= (1 + occnot) / (1 + ((float)totalobjs - numobjs));
    }

    // P(adj | object)*P(object)
    float bayesnum = pagiveno * po;
    // P(adj | object)*P(object) + P(adj | ~object)*P(~object)
    float bayesdenom = bayesnum + pagivennoto * (1 - po);
    return bayesnum / bayesdenom;
}
```

This final function calls `calcprob()` to determine the most probable object given a set of adjectives.
```
string mostprob(set<string> adj, map<string, vector<set<string>>> naive, int totalobjs) {
    float moreprob = -1;
    string probobj = "";
    for (map<string, vector<set<string>>>::iterator it = naive.begin(); it != naive.end(); it++) {
        float prob = calcprob(it->first, adj, totalobjs, naive);
        if (prob > moreprob) {
            moreprob = prob;
            probobj = it->first;
        }
    }

    return probobj;
}
```
