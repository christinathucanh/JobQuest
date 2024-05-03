## 📋 <a name="table">Table of Contents</a>

1. 🤖 [Introduction](#introduction)
2. ⚙️  [Tech Stack](#tech-stack)
3. 🔋 [Features](#features)
4. 🤸 [Quick Start](#quick-start)
5. 🕸️ [Snippets](#snippets)

## <a name="introduction">🤖 Introduction</a>

A hands-on experience in React Native development, from understanding the basics to building a feature-rich app with a focus on UI/UX, external data integration, and best practices.

<img width="1440" alt="Screenshot 2024-05-02 at 9 37 04 PM" src="https://github.com/christinathucanh/JobQuest/assets/95081865/bbb428c0-7fb0-4bcb-9343-17089b477854">
<img width="1440" alt="Screenshot 2024-05-02 at 9 37 25 PM" src="https://github.com/christinathucanh/JobQuest/assets/95081865/fb1af06f-3e0e-42e8-875e-e412d14e8e20">
<img width="1440" alt="Screenshot 2024-05-02 at 9 37 45 PM" src="https://github.com/christinathucanh/JobQuest/assets/95081865/273beb75-a1d2-48aa-823b-87d8b4a449ad">
<img width="1440" alt="Screenshot 2024-05-02 at 9 38 07 PM" src="https://github.com/christinathucanh/JobQuest/assets/95081865/9ea0b4d5-ff57-4097-a8b8-9fbac1ccf4d0">


## <a name="tech-stack">⚙️ Tech Stack</a>

- Node.js
- React Native
- Axios
- Expo
- Stylesheet

## <a name="features">🔋 Features</a>

👉 **Visually Appealing UI/UX Design**: Develop an aesthetically pleasing user interface using React Native components.

👉 **Third Party API Integration**: Fetch data from an external API and seamlessly integrate it into the app.

👉 **Search & Pagination Functionality**: Implement search functionality and pagination for efficient data navigation.

👉 **Custom API Data Fetching Hooks**:Create custom hooks for streamlined and reusable API data fetching.

👉 **Dynamic Home Page**: Explore diverse jobs from popular and nearby locations across different categories.

👉 **Browse with Ease on Explore Page**: Page: Navigate through various jobs spanning different categories and types.

👉 **Detailed Job Insights**: View comprehensive job details, including application links, salary info, responsibilities, and qualifications.

👉 **Tailored Job Exploration**: Find jobs specific to a particular title 

👉 **Robust Loading and Error Management**: Ensure effective handling of loading processes and error scenarios. 

👉 **Optimized for All Devices**: A responsive design for a seamless user experience across various devices.

and many more, including code architecture and reusability 

## <a name="quick-start">🤸 Quick Start</a>

Follow these steps to set up the project locally on your machine.

**Prerequisites**

Make sure you have the following installed on your machine:

- [Git](https://git-scm.com/)
- [Node.js](https://nodejs.org/en)
- [npm](https://www.npmjs.com/) (Node Package Manager)

**Cloning the Repository**

```bash
git clone https://github.com/adrianhajdin/project_react_native_jobs.git
cd project_react_native_jobs
```

**Installation**

Install the project dependencies using npm:

```bash
npm install
```

**Set Up Environment Variables**

Create a new file named `.env` in the root of your project and add the following content:

```env
X-RapidAPI-Key=
```

Replace the placeholder values with your actual credentials. You can obtain these credentials by signing up on the [RapidAPI website](https://rapidapi.com/letscrape-6bRBa3QguO5/api/jsearch).

**Running the Project**

```bash
npm start
```

Open [http://localhost:3000](http://localhost:3000) in your browser to view the project.

## <a name="snippets">🕸️ Snippets</a>

<details>
<summary><code>Search.js</code></summary>

```javascript
import React, { useEffect, useState } from 'react'
import { ActivityIndicator, FlatList, Image, TouchableOpacity, View } from 'react-native'
import { Stack, useRouter, useSearchParams } from 'expo-router'
import { Text, SafeAreaView } from 'react-native'
import axios from 'axios'

import { ScreenHeaderBtn, NearbyJobCard } from '../../components'
import { COLORS, icons, SIZES } from '../../constants'
import styles from '../../styles/search'

const JobSearch = () => {
    const params = useSearchParams();
    const router = useRouter()

    const [searchResult, setSearchResult] = useState([]);
    const [searchLoader, setSearchLoader] = useState(false);
    const [searchError, setSearchError] = useState(null);
    const [page, setPage] = useState(1);

    const handleSearch = async () => {
        setSearchLoader(true);
        setSearchResult([])

        try {
            const options = {
                method: "GET",
                url: `https://jsearch.p.rapidapi.com/search`,
                headers: {
                    "X-RapidAPI-Key": '',
                    "X-RapidAPI-Host": "jsearch.p.rapidapi.com",
                },
                params: {
                    query: params.id,
                    page: page.toString(),
                },
            };

            const response = await axios.request(options);
            setSearchResult(response.data.data);
        } catch (error) {
            setSearchError(error);
            console.log(error);
        } finally {
            setSearchLoader(false);
        }
    };

    const handlePagination = (direction) => {
        if (direction === 'left' && page > 1) {
            setPage(page - 1)
            handleSearch()
        } else if (direction === 'right') {
            setPage(page + 1)
            handleSearch()
        }
    }

    useEffect(() => {
        handleSearch()
    }, [])

    return (
        <SafeAreaView style={{ flex: 1, backgroundColor: COLORS.lightWhite }}>
            <Stack.Screen
                options={{
                    headerStyle: { backgroundColor: COLORS.lightWhite },
                    headerShadowVisible: false,
                    headerLeft: () => (
                        <ScreenHeaderBtn
                            iconUrl={icons.left}
                            dimension='60%'
                            handlePress={() => router.back()}
                        />
                    ),
                    headerTitle: "",
                }}
            />

            <FlatList
                data={searchResult}
                renderItem={({ item }) => (
                    <NearbyJobCard
                        job={item}
                        handleNavigate={() => router.push(`/job-details/${item.job_id}`)}
                    />
                )}
                keyExtractor={(item) => item.job_id}
                contentContainerStyle={{ padding: SIZES.medium, rowGap: SIZES.medium }}
                ListHeaderComponent={() => (
                    <>
                        <View style={styles.container}>
                            <Text style={styles.searchTitle}>{params.id}</Text>
                            <Text style={styles.noOfSearchedJobs}>Job Opportunities</Text>
                        </View>
                        <View style={styles.loaderContainer}>
                            {searchLoader ? (
                                <ActivityIndicator size='large' color={COLORS.primary} />
                            ) : searchError && (
                                <Text>Oops something went wrong</Text>
                            )}
                        </View>
                    </>
                )}
                ListFooterComponent={() => (
                    <View style={styles.footerContainer}>
                        <TouchableOpacity
                            style={styles.paginationButton}
                            onPress={() => handlePagination('left')}
                        >
                            <Image
                                source={icons.chevronLeft}
                                style={styles.paginationImage}
                                resizeMode="contain"
                            />
                        </TouchableOpacity>
                        <View style={styles.paginationTextBox}>
                            <Text style={styles.paginationText}>{page}</Text>
                        </View>
                        <TouchableOpacity
                            style={styles.paginationButton}
                            onPress={() => handlePagination('right')}
                        >
                            <Image
                                source={icons.chevronRight}
                                style={styles.paginationImage}
                                resizeMode="contain"
                            />
                        </TouchableOpacity>
                    </View>
                )}
            />
        </SafeAreaView>
    )
}

export default JobSearch
```
</details>

#
